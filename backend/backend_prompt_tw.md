## 目錄結構
backend/
  ├app/
     ├api/ 
     ├core/ #  核心功能模組，存放支撐整個專案運行的基礎功能和全域設定，通常具有高複用性和低業務耦合性。
     ├models/ # 資料模型模組，負責定義資料結構和資料庫表結構，通常與業務邏輯解耦。            
     └services/ # 業務邏輯模組，封裝具體的業務操作，通常依賴 models 和 core 完成複雜功能。


## 1. API設計
-設定API路由和控制器
-實現請求驗證
-新增身分驗證中介軟體
-設定速率限制
-實現錯誤處理
-新增日誌和監控
### 1.1 會員照片上傳
- **POST** `/api/members/upload`
- **請求格式**：FormData: { folder.zip }
- **回應格式**：
```json
{
  "code": 200,
  "message": "上傳成功",
  "data": {
    "total_members": 50,
    "success_count": 48,
    "failed_list": ["張三", "李四"],
    "training_status": "已觸發"
  }
}
```

### 1.2 人臉辨識查詢
- **POST** `/api/face/recognize`
- **請求格式**：
```json
{
  "image": "base64_encoded_image",
  "camera_id": "cam_001"
}
```
- **回應格式**：
```json
{
  "code": 200,
  "message": "辨識成功",
  "data": {
    "member_id": 12345,
    "name": "王小明",
    "is_vip": true,
    "confidence": 0.95,
    "last_visit": "2024-01-15"
  }
}
```

### 1.3 VIP語音歡迎
- **POST** `/api/vip/greet`
- **請求格式**：
```json
{
  "member_id": 12345,
  "template_id": "welcome_01"
}
```
- **回應格式**：
```json
{
  "code": 200,
  "message": "語音播放成功",
  "data": {
    "audio_url": "/audio/greet_12345.wav",
    "duration": 3.5
  }
}
```

### 1.4 會員資料管理
- **GET** `/api/members` - 查詢會員列表
- **GET** `/api/members/{id}` - 查詢特定會員
- **PUT** `/api/members/{id}` - 更新會員資訊
- **DELETE** `/api/members/{id}` - 刪除會員

### 1.5 系統監控
- **GET** `/api/system/status` - 系統狀態查詢
- **GET** `/api/system/metrics` - 效能指標
- **POST** `/api/system/backup` - 觸發備份
- **GET** `/api/logs/audit` - 稽核日誌查詢

### 1.6 權限管理
- **GET** `/api/users` - 用戶列表
- **POST** `/api/users` - 新增用戶
- **PUT** `/api/users/{id}/permissions` - 設定權限
- **GET** `/api/permissions` - 權限列表

## 2. 資料庫設計
-設定資料庫模型和架構
-實現資料關聯
-建立資料庫遷移
-新增資料驗證
-優化查詢效能
-實現資料快取
### 2.1 會員表（members）
| 欄位         | 型別           | 說明           | 約束條件       |
|--------------|----------------|----------------|----------------|
| id           | bigint         | 主鍵           | AUTO_INCREMENT |
| name         | varchar(50)    | 會員姓名       | NOT NULL       |
| phone        | varchar(20)    | 聯絡電話       | UNIQUE         |
| email        | varchar(100)   | 電子郵件       |                |
| is_vip       | tinyint        | 是否VIP        | DEFAULT 0      |
| vip_level    | int            | VIP等級        | DEFAULT 1      |
| photo_path   | varchar(255)   | 照片路徑       |                |
| face_vector  | blob           | 人臉特徵向量   |                |
| last_visit   | timestamp      | 最後到店時間   |                |
| visit_count  | int            | 到店次數       | DEFAULT 0      |
| notes        | text           | 備註           |                |
| status       | tinyint        | 狀態(啟用/停用)| DEFAULT 1      |
| created_at   | timestamp      | 建立時間       | DEFAULT NOW()  |
| updated_at   | timestamp      | 更新時間       | ON UPDATE NOW()|

### 2.2 辨識紀錄表（face_logs）
| 欄位         | 型別           | 說明           | 約束條件       |
|--------------|----------------|----------------|----------------|
| id           | bigint         | 主鍵           | AUTO_INCREMENT |
| member_id    | bigint         | 會員ID         | FOREIGN KEY    |
| recognized_at| timestamp      | 辨識時間       | DEFAULT NOW()  |
| camera_id    | varchar(50)    | 攝影機編號     | NOT NULL       |
| confidence   | float          | 相似度         |                |
| is_vip       | tinyint        | 是否VIP        |                |
| greeting_played| tinyint      | 是否播放歡迎語 | DEFAULT 0      |
| image_path   | varchar(255)   | 辨識圖片路徑   |                |
| processing_time| float        | 處理時間(秒)   |                |

### 2.3 管理員表（admins）
| 欄位         | 型別           | 說明           | 約束條件       |
|--------------|----------------|----------------|----------------|
| id           | bigint         | 主鍵           | AUTO_INCREMENT |
| username     | varchar(50)    | 帳號           | UNIQUE         |
| password     | varchar(100)   | 密碼(加密)     | NOT NULL       |
| email        | varchar(100)   | 電子郵件       |                |
| role         | varchar(20)    | 角色           | NOT NULL       |
| permissions  | json           | 權限設定       |                |
| last_login   | timestamp      | 最後登入時間   |                |
| login_count  | int            | 登入次數       | DEFAULT 0      |
| status       | tinyint        | 狀態           | DEFAULT 1      |
| created_at   | timestamp      | 建立時間       | DEFAULT NOW()  |
| updated_at   | timestamp      | 更新時間       | ON UPDATE NOW()|

### 2.4 系統設定表（system_config）
| 欄位         | 型別           | 說明           | 約束條件       |
|--------------|----------------|----------------|----------------|
| id           | bigint         | 主鍵           | AUTO_INCREMENT |
| config_key   | varchar(100)   | 設定鍵值       | UNIQUE         |
| config_value | text           | 設定內容       |                |
| description  | varchar(255)   | 設定說明       |                |
| updated_by   | bigint         | 更新者ID       |                |
| updated_at   | timestamp      | 更新時間       | ON UPDATE NOW()|

### 2.5 稽核日誌表（audit_logs）
| 欄位         | 型別           | 說明           | 約束條件       |
|--------------|----------------|----------------|----------------|
| id           | bigint         | 主鍵           | AUTO_INCREMENT |
| user_id      | bigint         | 操作者ID       |                |
| action       | varchar(100)   | 操作動作       | NOT NULL       |
| resource     | varchar(100)   | 操作資源       |                |
| resource_id  | bigint         | 資源ID         |                |
| old_values   | json           | 變更前數值     |                |
| new_values   | json           | 變更後數值     |                |
| ip_address   | varchar(45)    | IP位址         |                |
| user_agent   | text           | 使用者代理     |                |
| created_at   | timestamp      | 操作時間       | DEFAULT NOW()  |

## 3.安全實現
-實現使用者身分驗證
-設定基於角色的授權
-新增API安全措施
-設定CORS和CSP
-實現資料加密
-新增安全標頭和防護
