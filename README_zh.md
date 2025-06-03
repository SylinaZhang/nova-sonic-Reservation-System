## 项目概述

这是一个使用亚马逊 Nova Sonic 模型实现的酒店预订系统演示，通过语音交互实
现客户服务功能。该系统允许用户通过网页界面进行自然对话，完成创建新预订、
查询现有预订以及取消预订等操作。

## 核心组件

1. 服务器端组件：
   • **server.ts**：主服务器应用程序
     • 设置 Express 和 Socket.IO 实现实时通信
     • 创建和管理 Nova Sonic 双向流会话
     • 处理音频流的 WebSocket 连接
     • 管理会话生命周期和清理不活跃会话

   • **client.ts**：实现 NovaSonicBidirectionalStreamClient 类
     • 管理与 AWS Bedrock Nova Sonic API 的通信
     • 处理音频数据流和接收响应
     • 管理会话状态和事件

   • **hotel-confirmation.ts**：包含预订系统的业务逻辑
     • 定义数据模型（预订、取消政策）
     • 实现三个主要工具：
       • getReservation：通过姓名和入住日期查询现有预订
       • createReservation：创建新的酒店预订
       • cancelReservation：处理取消请求并计算退款

2. 客户端组件：
   • **index.html**：简单的网页界面，带有音频流控制
   • **main.js**：客户端 JavaScript
     • 管理与服务器的 WebSocket 连接
     • 处理麦克风访问和音频流
     • 处理来自 Nova Sonic 的响应
     • 更新对话历史界面

3. 配置和常量：
   • **consts.ts**：包含重要配置设置
     • Nova Sonic 的默认推理参数
     • 音频输入/输出配置
     • 三个工具的 JSON 模式
     • 定义 AI 助手行为和能力的系统提示

## 主要功能

1. 双向音频流：
   • 实时捕获用户麦克风音频
   • 流式传输到 AWS Bedrock Nova Sonic 模型
   • 接收并播放音频响应

2. 工具集成：
   • Nova Sonic 可以调用三个工具与预订系统交互：
     • 通过客户姓名和入住日期查询预订
     • 创建包含所有必要详细信息的新预订
     • 处理取消并计算适当的退款

3. 对话流程：
   • 系统提示引导 Nova Sonic 遵循特定的对话结构
   • 访问预订详情前验证客户身份
   • 处理取消前需要明确确认
   • 清晰解释取消政策和退款计算

4. 会话管理：
   • 创建和跟踪用户会话
   • 适当清理不活跃会话
   • 优雅处理断开连接

## 使用说明

### 前提条件
- Node.js（v14 或更高版本）
- 具有 Bedrock 访问权限的 AWS 账户
- 配置了适当凭证的 AWS CLI
- 支持 WebAudio API 的现代网页浏览器

**所需软件包：**

```json
{
  "dependencies": {
    "@aws-sdk/client-bedrock-runtime": "^3.785",
    "@aws-sdk/client-bedrock-agent-runtime": "^3.782",
    "@aws-sdk/credential-providers": "^3.782",
    "@smithy/node-http-handler": "^4.0.4",
    "@smithy/types": "^4.1.0",
    "@types/express": "^5.0.0",
    "@types/node": "^22.13.9",
    "axios": "^1.6.2",
    "dotenv": "^16.3.1",
    "express": "^4.21.2",
    "pnpm": "^10.6.1",
    "rxjs": "^7.8.2",
    "socket.io": "^4.8.1",
    "ts-node": "^10.9.2",
    "uuid": "^11.1.0"
  }
}
```

### 安装
1. 克隆仓库：
```bash
git clone <仓库URL>
cd <仓库名称>
```

2. 安装依赖：
```bash
npm install
```

3. 配置 AWS 凭证：
```bash
# 使用您的凭证配置 AWS CLI
aws configure --profile bedrock-test
```

4. 构建 TypeScript 代码：
```bash
npm run build
```

### 快速开始
1. 启动服务器：
```bash
npm start
```

2. 打开浏览器：
```
http://localhost:3000
```

3. 在提示时授予麦克风权限。

### 示例对话流程

以下是一个对话流程示例，展示了在不到10次交流中完成取消和创建新预订的过程：

1. **客户**："您好，我需要取消当前预订并创建一个新的预订。"
2. **助手**："您好！我很乐意帮助您处理这两项事务。请提供您要取消的预订的姓名和入住日期好吗？"
3. **客户**："我的名字是Angela Park，入住日期是2025年4月12日。"
4. **助手**："谢谢您，Angela。我找到了您在海景酒店的豪华海景房预订，入住时间为4月12日至15日。如果在4月5日前取消，您可以获得全额退款；如果在4月10日前取消，可获得50%退款。您确定要继续取消吗？"
5. **客户**："是的，请取消，我想在山林小屋预订5月20日至25日的标准大床房。"
6. **助手**："我已处理您的取消请求。您的取消确认代码是CANX-123456，全额退款750.00美元将在5-7个工作日内处理。关于您的新预订，山林小屋5月20日至25日的标准大床房价格为950.00美元。您确认要预订吗？"
7. **客户**："是的，请预订。"
8. **助手**："太好了！我已创建您的新预订。您的确认代码是CONF-789012。您的山林小屋标准大床房已确认，入住时间为2025年5月20日至25日，总价950.00美元。今天还有什么可以帮到您的吗？"

### 故障排除
1. 麦克风访问问题
- 问题：浏览器显示"麦克风权限被拒绝"
- 解决方案：
  ```javascript
  // 检查是否授予了麦克风权限
  const permissions = await navigator.permissions.query({ name: 'microphone' });
  if (permissions.state === 'denied') {
    console.error('需要麦克风访问权限');
  }
  ```

2. 音频播放问题
- 问题：没有音频输出
- 解决方案：
  ```javascript
  // 验证AudioContext是否已初始化
  if (audioContext.state === 'suspended') {
    await audioContext.resume();
  }
  ```

3. 连接问题
- 检查服务器日志的连接状态
- 验证WebSocket连接：
  ```javascript
  socket.on('connect_error', (error) => {
    console.error('连接失败:', error);
  });
  ```

## 项目结构

### 核心组件

1. **服务器端组件**：
   - **server.ts**：主服务器应用程序，设置Express和Socket.IO，管理Nova Sonic会话
   - **client.ts**：实现与AWS Bedrock Nova Sonic API的通信
   - **hotel-confirmation.ts**：包含预订系统的业务逻辑和工具实现

2. **客户端组件**：
   - **index.html**：网页界面
   - **main.js**：处理用户交互和音频流

3. **配置和类型**：
   - **consts.ts**：包含系统提示和工具模式的配置
   - **types.ts**：定义TypeScript类型

## 工作原理

系统通过以下流程工作：

1. 用户通过网页界面启动对话
2. 客户端捕获用户的音频并通过WebSocket发送到服务器
3. 服务器将音频流式传输到Nova Sonic模型
4. Nova Sonic处理音频并生成响应或调用工具
5. 如果需要工具调用，系统执行相应操作（查询、创建或取消预订）
6. Nova Sonic生成适当的响应，服务器将其发送回客户端
7. 客户端播放音频响应并更新界面

这种架构实现了流畅的对话体验，使客户能够通过自然语言完成复杂的预订任务。
