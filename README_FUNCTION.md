# HealthyLife 健康生活应用 - 功能解析文档

## 项目概述

HealthyLife是一款基于HarmonyOS原生开发的健康生活任务管理应用，帮助用户养成良好的健康生活习惯。应用采用模块化架构设计，支持任务打卡、进度追踪、成就系统等核心功能。

---

## 技术栈

| 技术项 | 版本/详情 |
|--------|-----------|
| 开发语言 | ArkTS（TypeScript超集） |
| 目标框架 | HarmonyOS 6.0.2 (API 22) |
| 兼容版本 | HarmonyOS 6.0.0 (API 20) 及以上 |
| 构建工具 | Hvigor |
| UI范式 | ArkTS声明式开发范式 |
| 数据存储 | 关系型数据库（Rdb） |
| 轻量存储 | 首选项（Preferences） |
| 测试框架 | @ohos/hypium、@ohos/hamock |

---

## 项目架构

### 目录结构

```
HealthyLife/
├── AppScope/                    # 应用全局配置
│   ├── app.json5                # 应用配置（包名、版本）
│   └── resources/               # 全局资源（字符串、图片）
├── commons/                     # 公共模块
│   └── common/                  # 公共功能模块
│       └── src/main/ets/
│           ├── constants/       # 常量定义
│           ├── database/        # 数据库操作
│           ├── model/           # 数据模型
│           └── utils/           # 工具类
├── features/                    # 功能特性模块
│   └── healthylife/             # 健康生活功能模块
│       └── src/main/ets/
│           ├── healthylifeability/  # 功能入口
│           ├── model/           # 业务模型
│           ├── pages/           # 页面
│           ├── viewmodel/       # 视图模型
│           └── views/           # UI组件
├── products/                    # 产品入口模块
│   └── default/                 # 默认产品
│       └── src/main/
│           ├── ets/             # 源代码
│           ├── module.json5     # 模块配置
│           └── resources/       # 资源文件
├── screenshots/                 # 应用截图
├── build-profile.json5          # 构建配置
├── oh-package.json5             # 依赖配置
└── hvigorfile.ts                # 构建脚本
```

### 模块依赖关系

```
products/default（入口模块）
    ├── depends on: commons/common
    └── depends on: features/healthylife
                        └── depends on: commons/common
```

---

## 核心功能模块

### 1. 任务管理系统

#### 1.1 支持的任务类型

| 任务ID | 任务名称 | 目标类型 | 目标范围 | 打卡方式 |
|--------|----------|----------|----------|----------|
| 0 | 早起 | 时间 | 06:00 - 09:00 | 时间打卡 |
| 1 | 喝水 | 数量 | 0.5L - 3L | 多次累计 |
| 2 | 吃苹果 | 数量 | 1个 - 5个 | 多次累计 |
| 3 | 每日微笑 | 次 | 1次 | 单次打卡 |
| 4 | 刷牙 | 次 | 1次 | 单次打卡 |
| 5 | 早睡 | 时间 | 20:00 - 23:00 | 时间打卡 |

#### 1.2 任务功能特性

- **任务创建**：用户可选择开启/关闭任务，设置目标值
- **目标设置**：
  - 时间类型：通过时间选择器设置目标时间
  - 数量类型：通过滚动选择器选择目标数量
- **打卡记录**：
  - 单次打卡：一次完成（微笑、刷牙）
  - 多次打卡：累计完成（喝水、吃苹果）
- **进度显示**：实时显示当日任务完成进度百分比

#### 1.3 任务数据模型

```
TaskInfo {
  taskId: number           // 任务ID（0-5）
  isOpen: boolean          // 是否开启
  targetValue: string      // 目标值
  finValue: string         // 已完成值
  isDone: boolean          // 是否完成
  isAlert: boolean         // 是否开启提醒
  alertStartTime: string   // 提醒开始时间
  alertEndTime: string     // 提醒结束时间
  alertFrequency: string   // 提醒频率
  reminderId: number       // 提醒ID
}
```

### 2. 打卡功能

#### 2.1 打卡流程

```
用户点击任务 → 弹出打卡对话框 → 选择打卡时间/数量 → 记录到数据库 → 更新进度
```

#### 2.2 打卡对话框类型

| 类型 | 适用任务 | 功能说明 |
|------|----------|----------|
| 时间选择器 | 早起、早睡 | 选择打卡时间，验证是否在目标范围内 |
| 数量选择器 | 喝水、吃苹果 | 选择本次完成数量，累计到目标值 |
| 确认对话框 | 微笑、刷牙 | 一键打卡确认 |

### 3. 进度追踪系统

#### 3.1 首页进度展示

- **顶部进度环**：显示当日总体完成进度（已完成任务数/总任务数）
- **周视图日历**：查看本周每日完成情况
- **任务列表**：显示每个任务的完成状态和进度

#### 3.2 历史记录

- 支持查看历史日期的任务完成情况
- 周视图快速切换日期
- 每日完成状态可视化展示

### 4. 成就系统

#### 4.1 成就等级

| 连续打卡天数 | 成就等级 |
|--------------|----------|
| 3天 | 初级成就 |
| 7天 | 进阶成就 |
| 30天 | 高级成就 |
| 50天 | 资深成就 |
| 73天 | 专家成就 |
| 99天 | 大师成就 |

#### 4.2 成就功能

- **连续打卡统计**：自动计算连续完成任务的天数
- **成就弹窗**：获得新成就时展示动画效果
- **成就页面**：查看所有已获得的成就列表
- **数据持久化**：通过首选项存储成就数据

### 5. 服务卡片功能

#### 5.1 支持的卡片类型

| 卡片名称 | 尺寸 | 功能说明 |
|----------|------|----------|
| 任务列表卡片 | 1x2 | 显示任务列表快速入口 |
| 进度卡片 | 2x2 | 显示今日任务完成进度 |

#### 5.2 卡片特性

- 实时同步任务数据
- 支持快速打卡操作
- 自动更新显示状态

### 6. 通知提醒功能

#### 6.1 提醒类型

- **早起提醒**：在设定时间范围开始时提醒
- **早睡提醒**：在设定时间范围开始时提醒

#### 6.2 提醒设置

- 提醒时间范围设置
- 提醒频率配置
- 后台代理提醒（需权限：`ohos.permission.PUBLISH_AGENT_REMINDER`）

---

## 数据库设计

### 数据库信息

- 数据库名称：`HealthyLife.db`
- 数据库类型：关系型数据库（Rdb）

### 数据表结构

#### 1. 日期信息表 (dayInfo)

| 字段名 | 类型 | 说明 |
|--------|------|------|
| date | string | 日期（主键，格式：YYYY-MM-DD） |
| targetTaskNum | number | 当日目标任务数 |
| finTaskNum | number | 当日完成任务数 |

#### 2. 任务信息表 (taskInfo)

| 字段名 | 类型 | 说明 |
|--------|------|------|
| taskId | number | 任务ID（主键，0-5） |
| isOpen | boolean | 是否开启 |
| targetValue | string | 目标值 |
| finValue | string | 已完成值 |
| isDone | boolean | 是否完成 |
| isAlert | boolean | 是否开启提醒 |
| alertStartTime | string | 提醒开始时间 |
| alertEndTime | string | 提醒结束时间 |
| alertFrequency | string | 提醒频率 |
| reminderId | number | 提醒ID |

#### 3. 当日任务信息表 (dayTaskInfo)

| 字段名 | 类型 | 说明 |
|--------|------|------|
| id | number | 主键（自增） |
| date | string | 日期 |
| taskId | number | 任务ID |
| targetValue | string | 目标值 |
| finValue | string | 已完成值 |
| isDone | boolean | 是否完成 |

#### 4. 服务卡片信息表 (formInfo)

| 字段名 | 类型 | 说明 |
|--------|------|------|
| formId | string | 卡片ID（主键） |
| formName | string | 卡片名称 |
| formDimension | number | 卡片尺寸 |

---

## 主要页面与组件

### 页面结构

```
应用入口 (EntryAbility)
    ↓
启动页 (SplashPage) → 广告页 (AdvertisingPage)
    ↓
主页面 (Index) → HealthyLifePage (Tab导航)
    ├── 首页 (HomeComponent)
    │   ├── 顶部进度组件 (HomeTopComponent)
    │   ├── 周视图日历 (WeekCalendarComponent)
    │   └── 任务列表 (TaskListComponent)
    └── 我的 (MineComponent)
        ├── 用户信息 (UserInfoComponent)
        └── 菜单列表
```

### 核心组件说明

| 组件名 | 文件路径 | 功能说明 |
|--------|----------|----------|
| HomeComponent | features/healthylife/src/main/ets/views/HomeComponent.ets | 首页主容器，包含进度、日历、任务列表 |
| HomeTopComponent | features/healthylife/src/main/ets/views/home/HomeTopComponent.ets | 首页顶部进度环和日期显示 |
| TaskListComponent | features/healthylife/src/main/ets/views/home/TaskListComponent.ets | 任务列表展示和交互 |
| WeekCalendarComponent | features/healthylife/src/main/ets/views/home/WeekCalendarComponent.ets | 周视图日历，支持日期切换 |
| MineComponent | features/healthylife/src/main/ets/views/MineComponent.ets | 个人中心页面 |
| AchievementComponent | features/healthylife/src/main/ets/views/AchievementComponent.ets | 成就展示页面 |
| AddTaskComponent | features/healthylife/src/main/ets/views/task/AddTaskComponent.ets | 添加任务页面 |
| EditTaskComponent | features/healthylife/src/main/ets/views/task/EditTaskComponent.ets | 编辑任务页面 |

### 对话框组件

| 对话框 | 功能说明 |
|--------|----------|
| TaskClockCustomDialog | 任务打卡弹窗 |
| TargetSettingDialog | 目标设置弹窗 |
| AchievementDialog | 成就获得弹窗 |
| UserPrivacyDialog | 用户隐私协议弹窗 |

---

## 工具类说明

### 数据库工具

| 工具类 | 文件路径 | 功能说明 |
|--------|----------|----------|
| RdbUtils | commons/common/src/main/ets/database/RdbUtils.ets | 数据库操作通用工具 |
| DayInfoApi | commons/common/src/main/ets/database/tables/DayInfoApi.ets | 日期信息CRUD操作 |
| TaskInfoApi | commons/common/src/main/ets/database/tables/TaskInfoApi.ets | 任务信息CRUD操作 |
| DayTaskInfoApi | commons/common/src/main/ets/database/tables/DayTaskInfoApi.ets | 当日任务CRUD操作 |
| FormInfoApi | commons/common/src/main/ets/database/tables/FormInfoApi.ets | 服务卡片CRUD操作 |

### 其他工具类

| 工具类 | 功能说明 |
|--------|----------|
| PreferencesUtils | 用户首选项存储工具 |
| FormUtils | 服务卡片工具类 |
| AgentUtils | 代理提醒工具类 |
| RequestAuthorization | 权限请求工具类 |
| PromptActionClass | 自定义弹窗工具类 |
| Utils | 通用工具（时间转换等） |

---

## 应用权限

| 权限 | 说明 | 用途 |
|------|------|------|
| ohos.permission.PUBLISH_AGENT_REMINDER | 允许应用使用后台代理权限 | 早起/早睡任务提醒通知 |

---

## 应用配置

### 应用信息

```
包名：com.huawei.healthylife
版本号：1000000
版本名：1.0.0
```

### 构建配置

- SDK版本：API 22 (HarmonyOS 6.0.2)
- 最低兼容版本：API 20 (HarmonyOS 6.0.0)
- 构建工具：Hvigor
- 开发工具：DevEco Studio 6.0.2 Release及以上

---

## 使用指南

### 开发环境搭建

1. 安装DevEco Studio 6.0.2 Release或更高版本
2. 配置HarmonyOS SDK API 22
3. 克隆项目到本地
4. 使用DevEco Studio打开项目
5. 等待项目同步完成

### 运行应用

1. 连接HarmonyOS设备或启动模拟器
2. 点击运行按钮安装应用
3. 应用启动后显示启动页和广告页
4. 同意隐私协议后进入主界面

### 核心操作流程

#### 添加任务

```
首页 → 点击右下角"+"按钮 → 添加任务页面 → 选择任务 → 设置目标 → 保存
```

#### 任务打卡

```
首页 → 点击任务卡片 → 打卡对话框 → 选择时间/数量 → 确认打卡
```

#### 查看历史

```
首页 → 周视图日历 → 点击历史日期 → 查看当日任务完成情况
```

#### 添加桌面卡片

```
长按应用图标 → 服务卡片 → 选择卡片类型 → 添加到桌面
```

---

## 项目特色

### 1. 模块化架构

- 清晰的三层模块划分：commons（公共层）→ features（功能层）→ products（产品层）
- 模块间依赖关系明确，便于代码复用和维护
- 支持多产品配置，可扩展性强

### 2. 声明式UI开发

- 使用ArkTS声明式开发范式，代码简洁高效
- 采用@State、@Provide、@Consume等装饰器管理状态
- 组件化开发，UI与逻辑分离

### 3. 数据持久化方案

- 关系型数据库（Rdb）：存储任务、日期等结构化数据
- 首选项（Preferences）：存储成就、用户设置等轻量数据
- 双重存储方案，满足不同数据场景需求

### 4. 服务卡片支持

- 支持1x2和2x2两种尺寸卡片
- 卡片数据实时同步
- 提供快速打卡入口，提升用户体验

### 5. 成就激励机制

- 六级成就体系，激励持续打卡
- 精美动画效果，增强成就感
- 连续打卡统计，培养健康习惯

---

## 文件索引

### 入口文件

| 文件 | 路径 |
|------|------|
| 应用入口 | products/default/src/main/ets/entryability/EntryAbility.ets |
| 主界面 | products/default/src/main/ets/pages/Index.ets |
| 主页面 | features/healthylife/src/main/ets/pages/HealthyLifePage.ets |
| 启动页 | products/default/src/main/ets/pages/SplashPage.ets |

### 配置文件

| 文件 | 路径 |
|------|------|
| 应用配置 | AppScope/app.json5 |
| 模块配置 | products/default/src/main/module.json5 |
| 构建配置 | build-profile.json5 |
| 依赖配置 | oh-package.json5 |

---

## 许可证

本项目基于 Apache License 2.0 许可证开源。

---

## 更新日志

### v1.0.0 (当前版本)

- 初始版本发布
- 支持6种健康生活任务
- 实现打卡和进度追踪功能
- 实现成就系统
- 支持服务卡片功能
- 支持通知提醒功能
