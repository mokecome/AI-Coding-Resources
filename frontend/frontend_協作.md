设计系统建设
• 团队技术栈熟悉度
• AI工具支持程度（React > Vue > Angular）
• 组件库生态（Ant Design、Material-UI等）

设计系统规划要点：
• 确定视觉风格（简约、商务、活泼等）
• 建立颜色语义化体系
• 定义间距和字体规范
• 规划组件层次结构


🔄 阶段3：迭代开发（效率提升）

代码质量检查清单：

• 使用了CSS变量而非硬编码
• 组件命名符合规范
• 实现了所有必要状态
• 添加了适当的注释
• 通过了TypeScript检查
✅ 阶段4：质量保证（用户体验）
确保最终产品的质量：

测试优先级：

1. 功能正确性（最高）
2. 视觉一致性（高）
3. 响应式适配（高）
4. 性能表现（中）
5. 无障碍支持（中）
🔄 阶段5：持续优化
反馈收集渠道：
• 开发团队使用体验
• 设计师审查意见
• 用户行为数据
• 性能监控指标




第1步：建立分层架构
// 项目结构
src/
├── components/     // UI层：手写核心组件
├── pages/         // UI层：AI生成页面布局
├── hooks/         // 交互层：手写复杂逻辑
├── services/      // 逻辑层：AI生成API调用
├── utils/         // 逻辑层：AI生成工具函数
└── types/         // 数据层：AI生成类型定义
'''
1. 基础设计变量系统
/* 颜色系统 */
:root {
/* 主色调 */
--primary-50: #f0f9ff;
--primary-500: #3b82f6;
--primary-900: #1e3a8a;

/* 语义化颜色 */
--success: #10b981;
--warning: #f59e0b;
--error: #ef4444;

/* 间距系统 */
--space-1: 0.25rem;  /* 4px */
--space-2: 0.5rem;   /* 8px */
--space-4: 1rem;     /* 16px */
--space-6: 1.5rem;   /* 24px */
--space-8: 2rem;     /* 32px */
}
2. 组件规范模板
每个组件都按照这个模板描述：

## 按钮组件 (Button)

### 基础样式
- 默认高度：40px
- 内边距：12px 24px
- 圆角：6px
- 字体：14px, 500字重

### 状态变化
- hover：背景色加深10%，添加阴影
- active：背景色加深20%
- disabled：透明度50%，禁用交互

### 尺寸规格
- small：32px高度，8px 16px内边距
- medium：40px高度，12px 24px内边距
- large：48px高度，16px 32px内边距

### 使用场景
- primary：主要操作按钮
- secondary：次要操作按钮
- ghost：轻量级操作按钮
3. 交互规范定义
/* 动画时长 */
:root {
--duration-fast: 150ms;
--duration-normal: 250ms;
--duration-slow: 350ms;

/* 缓动函数 */
--ease-out: cubic-bezier(0.16, 1, 0.3, 1);
--ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);
}

/* 状态反馈 */
.button {
transition: all var(--duration-normal) var(--ease-out);
}

.button:hover {
transform: translateY(-1px);
box-shadow: 04px12pxrgba(0, 0, 0, 0.15);
}
'''

第2步：UI层混合开发

• 手写部分：核心组件（Button、Input、Modal等）
• AI生成部分：页面布局、列表展示、表单结构
• 设计稿识别：复杂页面先截图，让Claude识别生成初始代码

第3步：逻辑层AI编程：
// API调用封装
创建用户管理的service层，包含：
- getUserList: 获取用户列表，支持分页和搜索
- createUser: 创建用户，包含表单验证
- updateUser: 更新用户信息
- deleteUser: 删除用户，包含确认提示
使用axios，添加错误处理和loading状态

第4步：数据层类型定义
// TypeScript类型生成
根据后端API文档，生成完整的TypeScript类型定义：
- User接口类型
- API请求响应类型  
- 表单数据类型
- 分页参数类型