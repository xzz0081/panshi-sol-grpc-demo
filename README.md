# Panshi Solana gRPC 示例集合

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Rust](https://img.shields.io/badge/Rust-000000?logo=rust&logoColor=white)](https://www.rust-lang.org/)

一个全面的Solana区块链gRPC连接和数据流处理示例集合，支持TypeScript和Rust两种语言实现。

## 📋 目录

- [项目概述](#项目概述)
- [功能特性](#功能特性)
- [项目结构](#项目结构)
- [快速开始](#快速开始)
- [配置说明](#配置说明)
- [使用示例](#使用示例)
- [API文档](#api文档)
- [故障排除](#故障排除)
- [贡献指南](#贡献指南)

## 🚀 项目概述

本项目提供了一套完整的Solana区块链gRPC连接示例，帮助开发者快速上手Solana实时数据流处理。项目包含了从基础连接到高级功能的完整实现，适合不同层次的开发者学习和使用。

### 主要用途
- 🔗 建立稳定的Solana gRPC连接
- 📊 实时监控区块链数据流
- 🔄 实现自动重连机制
- 📈 处理交易和账户更新
- 🛠️ 提供生产级别的错误处理

## ✨ 功能特性

### 核心功能
- ✅ **多语言支持**: TypeScript 和 Rust 双重实现
- ✅ **实时数据流**: 支持交易、账户、区块等数据流
- ✅ **自动重连**: 内置断线重连机制
- ✅ **错误处理**: 完善的异常处理和日志记录
- ✅ **配置灵活**: 支持多种配置方式
- ✅ **生产就绪**: 经过实际项目验证的代码

### 高级特性
- 🔧 **自定义过滤器**: 支持复杂的数据过滤条件
- 📊 **性能监控**: 内置延迟和性能监控
- 🔐 **安全连接**: 支持TLS加密连接
- 📝 **详细日志**: 完整的操作日志记录

## 📁 项目结构

```
panshi-sol-grpc-demo/
├── README.md                          # 项目说明文档
├── LICENSE                            # 开源协议
├── .gitignore                         # Git忽略文件
├── Rust/                              # Rust实现
│   ├── making_a_grpc_connection/      # 基础gRPC连接
│   ├── adding_a_reconnection_mechanism/ # 重连机制实现
│   └── modifying_subscribe_request/   # 订阅请求修改
└── Typescript/                        # TypeScript实现
    ├── making_a_grpc_connection/      # 基础gRPC连接
    ├── add_a_reconnect_mechanism/     # 重连机制实现
    ├── modifying_subscribe_request/   # 订阅请求修改
    └── [GRPC]Streaming_Token_TXN/     # 代币交易流处理
```

## 🚀 快速开始

### 环境要求

#### TypeScript 项目
- Node.js >= 16.0.0
- npm >= 8.0.0 或 yarn >= 1.22.0
- TypeScript >= 4.9.0

#### Rust 项目
- Rust >= 1.70.0
- Cargo >= 1.70.0

### 安装步骤

#### 1. 克隆项目
```bash
git clone https://github.com/xzz0081/panshi-sol-grpc-demo.git
cd panshi-sol-grpc-demo
```

#### 2. TypeScript 项目设置
```bash
# 进入TypeScript示例目录
cd Typescript/making_a_grpc_connection

# 安装依赖
npm install

# 配置环境变量
cp .env.example .env
# 编辑 .env 文件，填入您的配置信息

# 运行项目
npm start
```

#### 3. Rust 项目设置
```bash
# 进入Rust示例目录
cd Rust/making_a_grpc_connection

# 构建项目
cargo build --release

# 运行项目
cargo run -- --endpoint "your-grpc-endpoint" --x-token "your-token"
```

## ⚙️ 配置说明

### 环境变量配置

创建 `.env` 文件并配置以下参数：

```env
# gRPC连接配置
GRPC_ENDPOINT=https://grpc.shyft.to
X_TOKEN=your_shyft_token_here

# 连接参数
COMMITMENT_LEVEL=processed
TIMEOUT_MS=10000
MAX_RETRIES=5

# 监控地址 (可选)
MONITOR_ADDRESS=your_solana_address_here

# 日志级别
LOG_LEVEL=info
```

### 配置参数说明

| 参数 | 说明 | 默认值 | 必需 |
|------|------|--------|------|
| `GRPC_ENDPOINT` | gRPC服务端点 | - | ✅ |
| `X_TOKEN` | 认证令牌 | - | ✅ |
| `COMMITMENT_LEVEL` | 确认级别 | `processed` | ❌ |
| `TIMEOUT_MS` | 连接超时时间(毫秒) | `10000` | ❌ |
| `MAX_RETRIES` | 最大重试次数 | `5` | ❌ |
| `MONITOR_ADDRESS` | 监控的Solana地址 | - | ❌ |
| `LOG_LEVEL` | 日志级别 | `info` | ❌ |

## 📖 使用示例

### 基础连接示例

#### TypeScript
```typescript
import Client from "@triton-one/yellowstone-grpc";

const client = new Client(
  process.env.GRPC_ENDPOINT!,
  process.env.X_TOKEN!,
  undefined
);

// 建立连接并订阅数据
const stream = await client.subscribe();
stream.on("data", (data) => {
  console.log("收到数据:", data);
});
```

#### Rust
```rust
use yellowstone_grpc_client::GeyserGrpcClient;

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    let client = GeyserGrpcClient::build_from_shared(endpoint)?
        .x_token(Some(token))?
        .connect()
        .await?;

    // 订阅数据流
    let (mut subscribe_tx, mut stream) = client.subscribe().await?;

    Ok(())
}
```

### 高级功能示例

#### 1. 自动重连机制
```typescript
async function connectWithRetry(maxRetries: number = 5) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      const client = new Client(endpoint, token);
      const stream = await client.subscribe();
      console.log(`连接成功 (尝试 ${attempt}/${maxRetries})`);
      return stream;
    } catch (error) {
      console.error(`连接失败 (尝试 ${attempt}/${maxRetries}):`, error);
      if (attempt < maxRetries) {
        await new Promise(resolve => setTimeout(resolve, 1000 * attempt));
      }
    }
  }
  throw new Error('达到最大重试次数，连接失败');
}
```

#### 2. 交易过滤和处理
```typescript
const subscribeRequest = {
  transactions: {
    "raydium_txns": {
      accountInclude: ["675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8"],
      accountExclude: [],
      accountRequired: []
    }
  },
  commitment: CommitmentLevel.PROCESSED
};

stream.on("data", (data) => {
  if (data.transaction) {
    const signature = data.transaction.transaction?.signature;
    console.log("Raydium交易:", signature);
  }
});
```

#### 3. 账户监控
```typescript
const accountSubscription = {
  accounts: {
    "token_accounts": {
      account: ["your_token_account_address"],
      owner: [],
      filters: []
    }
  }
};
```

## 🔧 API文档

### TypeScript API

#### Client 类
```typescript
class Client {
  constructor(endpoint: string, xToken: string, tls?: boolean)

  // 订阅数据流
  async subscribe(): Promise<ClientReadableStream>

  // 获取版本信息
  async getVersion(): Promise<VersionInfo>

  // 获取最新区块哈希
  async getLatestBlockhash(): Promise<BlockhashInfo>
}
```

#### 订阅请求接口
```typescript
interface SubscribeRequest {
  accounts?: { [key: string]: SubscribeRequestFilterAccounts };
  slots?: { [key: string]: SubscribeRequestFilterSlots };
  transactions?: { [key: string]: SubscribeRequestFilterTransactions };
  blocks?: { [key: string]: SubscribeRequestFilterBlocks };
  commitment?: CommitmentLevel;
}
```

### Rust API

#### GeyserGrpcClient
```rust
impl GeyserGrpcClient {
    // 构建客户端
    pub fn build_from_shared(endpoint: String) -> Result<GeyserGrpcClientBuilder>

    // 设置认证令牌
    pub fn x_token(self, token: Option<String>) -> Result<Self>

    // 建立连接
    pub async fn connect(self) -> Result<GeyserGrpcClient<impl Interceptor>>

    // 订阅数据流
    pub async fn subscribe(&self) -> Result<(Sender, Receiver)>
}
```

## 🛠️ 故障排除

### 常见问题

#### 1. 连接超时
**问题**: `Connection timeout after 10s`
**解决方案**:
- 检查网络连接
- 验证gRPC端点地址
- 增加超时时间设置

#### 2. 认证失败
**问题**: `Authentication failed`
**解决方案**:
- 验证X-Token是否正确
- 检查令牌是否过期
- 确认令牌权限设置

#### 3. 数据流中断
**问题**: `Stream disconnected unexpectedly`
**解决方案**:
- 启用自动重连机制
- 检查网络稳定性
- 调整心跳间隔设置

#### 4. 内存使用过高
**问题**: `High memory usage`
**解决方案**:
- 优化数据过滤条件
- 及时处理接收到的数据
- 设置合适的缓冲区大小

### 调试技巧

#### 启用详细日志
```bash
# TypeScript
DEBUG=* npm start

# Rust
RUST_LOG=debug cargo run
```

#### 性能监控
```typescript
// 监控连接状态
stream.on('status', (status) => {
  console.log('连接状态:', status);
});

// 监控错误
stream.on('error', (error) => {
  console.error('流错误:', error);
});
```

## 🤝 贡献指南

我们欢迎所有形式的贡献！请遵循以下步骤：

### 贡献流程
1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

### 代码规范
- 遵循现有代码风格
- 添加适当的注释和文档
- 确保所有测试通过
- 更新相关文档

### 报告问题
如果您发现bug或有功能建议，请：
1. 检查是否已有相关issue
2. 创建新issue并提供详细信息
3. 包含复现步骤和环境信息

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 🙏 致谢

- [Triton One](https://triton.one/) - 提供优秀的Yellowstone gRPC客户端
- [Solana Labs](https://solana.com/) - Solana区块链平台
- [Shyft](https://shyft.to/) - 提供可靠的gRPC服务

## 📞 联系我们

- 项目维护者: [@xzz0081](https://github.com/xzz0081)
- 问题反馈: [GitHub Issues](https://github.com/xzz0081/panshi-sol-grpc-demo/issues)
- 讨论交流: [GitHub Discussions](https://github.com/xzz0081/panshi-sol-grpc-demo/discussions)

---

⭐ 如果这个项目对您有帮助，请给我们一个星标！
