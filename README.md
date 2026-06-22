# LumiAgent CEF

LumiAgent CEF 是 LumiAgent 的 macOS 桌面客户端。应用由 CEF 原生宿主、Vue Agent 工作台和 AgentCore 运行时组成。CEF 只负责承载本地 UI 和 native bridge；产品界面是单一 Agent 工作台。

## 代码结构

```text
LumiAgent-CEF/
  native/              macOS CEF 宿主、系统集成、native bridge、AgentCore 适配层
  renderer/            Vue Agent 工作台、共享协议、runtime bridge、共享 UI
  external/AgentCore/  AgentCore 框架依赖
  third_party/cef/     CEF SDK
  references/          参考项目，不参与产品构建
  docs/                当前架构、开发、协议和运行时数据说明
  scripts/             本地开发脚本
```

## 运行链路

1. macOS app 启动 CEF 宿主。
2. CEF 通过 `lumi://agent/index.html` 加载 `renderer/dist/agent`。
3. Vue 工作台通过 `window.lumiAgentNative` 调用 native bridge。
4. native bridge 将命令分发到配置、状态库、Agent、MCP、技能、文件、终端和权限服务。
5. Agent 运行通过 AgentCore 执行，流式事件写入 SQLite 并发布给 renderer。

## 本地开发

安装 renderer 依赖：

```bash
cd renderer
npm install
```

构建 Agent 工作台：

```bash
scripts/build-renderer.sh
```

配置并构建 native：

```bash
scripts/configure-native.sh -DLUMI_NATIVE_ENABLE_CEF=ON
scripts/build-native.sh
```

默认 native 构建目录是 `build/native`。构建完成后，macOS app 位于 native 构建输出目录下的 `LumiAgent.app`。

## 文档

- [docs/architecture.md](docs/architecture.md)：整体架构和模块边界
- [docs/renderer-agent.md](docs/renderer-agent.md)：Agent renderer 目录和前端职责
- [docs/native-host.md](docs/native-host.md)：native 宿主、macOS 集成和 CEF 入口
- [docs/native-bridge.md](docs/native-bridge.md)：renderer/native 协议和命令分组
- [docs/runtime-data.md](docs/runtime-data.md)：本机数据目录、配置、SQLite 和凭据
- [docs/development.md](docs/development.md)：构建、验证和调试命令

## 边界约定

- Renderer 只通过 runtime bridge 访问宿主能力，不直接访问 C++、CEF 或本地文件系统。
- native 负责 CEF 生命周期、macOS 集成、持久化、权限和 AgentCore 适配。
- AgentCore 保持在 `external/AgentCore`，产品代码通过 native 适配层使用它。
- 用户数据写入 `$HOME/.lumi`，不写入仓库目录。# lumi-agent
# lumi-agent
# lumi-agent
