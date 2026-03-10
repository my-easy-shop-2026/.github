# My Easy Shop 2026

Josh 的 side project，一個以 Java 開發的簡易網路商店後端服務。
主要目標是提供可瀏覽，下單、支付與出貨的電商功能。

## 專案架構

1. 以openapi的code generator為基礎的document first development
2. 微服務架構，透過nacos支援服務發現、seata支援分散式事務
3. 以liquibase輔助sql migration



## 專案定位

這個專案聚焦在document first的微服務系統後端，強調：
- openapi驅動的文件第一開發：domain與controller都以文件自動生成，降低微服務串接的重複工作量
- seata驅動的分散式事務：非侵入性的分散式事務，減少人為對分散式事務的處理工作

## 核心功能

- 刊登商品
- 用戶登入登出
- 購買商品
- 跳轉第三方支付系統
- 商品出貨(開發中)
- 購買記錄查詢


## 技術棧

- Language: Java (`java 17`)
- API: Spring Boot 3.1.5
- DB: MySQL + MyBatis plus 3.5.3.2
- Cache: Redis
- Document: Openapi 3.0.3
- Service Discovery: Nacos 2.2.3
- Distribution Transaction: Seata 2.0.0

## 專案結構（節錄）

```text
src/main/java/{package}/
  common/         # 專案切面與套件設定
  controller/     # controller實作 (路徑定義來自openapi code generatoer)
  mapper/         # 資料存取 (資料定義來自openapi code generatoer)
  service/        # 商業邏輯功能實現

target/generated-sources/openapi/src/main/java/{package}
  api/            # 由openapi code generatoer自動生成的路徑定義
  model/          # 由openapi code generatoer自動生成的資料定義
```

## 模組介紹

common-module: 共用套件與母pom
nacos: 服務發現
seata: 分散式事務
sql-migration: 基於liquibase的資料庫遷移


## 快速啟動（本地）

```bash
cd api-server
cp .env.example .env
go run main.go serve
```

或使用 Docker：

```bash
cd api-server
docker compose -f docker/docker-compose.yml up --build
```

## 備註

這是 Josh 持續迭代中的 side project，目標是把 Vault 後端核心做成可擴充、可實戰的工程模板。
