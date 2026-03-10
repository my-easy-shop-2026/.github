# My Easy Shop 2026

![image](intro.jpg)

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
- Timed Task Management: Xxl 2.4.0

## 專案結構

![image](structure.jpg)

- Gateway Layer: 提供接口供外部串接，處理權限驗證、前端資料格式，未來規劃將包含權限管理、限流等功能
- Base Module: 處理商業邏輯
- Platform Service: 提供平台內管理與支援功能
- Infrasture: 基礎建設

## 模組介紹

- nacos: 服務發現
- seata: 分散式事務
- xxl: 定時任務管理
- sql-migration: 基於liquibase的資料庫遷移
- openapi: 所有api與data的文件定義

- common-module: 共用套件與母pom

- order-bll: 提供下單與支付相關api接口
- order-base: 下單與支付相關商業邏輯實作
- sku-bll: 提供產品列表相關api接口
- sku-base: 產品列表相關商業邏輯實作
- user-bll: 提供用戶注冊登入相關api接口
- user-base: 用戶注冊登入相關商業邏輯實作
- management-bll: 提供管理商品與更新物流資訊api接口
- management-base: 管理商品與更新物流資訊的商業邏輯實作
- logistics-base: 物流相關商業邏輯實作(開發中)
- notify-base: 發信相關商業邏輯實作
- agency-base: 提供外部平台用戶購買功能(未開發)


## 專案資料夾結構

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

## 快速啟動（本地）

1. 建立infrasture
  - 將mysql 8建立在3311 port，帳號root 密碼123456
  - 將redis 7建立在6379 port，無ssl，密碼123456

2. 啟動platform service
```sh
# nacos
cd nacos
mvn -Prelease-nacos -Dmaven.test.skip=true clean install -U 
cd distribution/target/nacos-server-2.2.3/nacos/bin
sh startup.sh -m standalone
## 建立後登入後台查看是否成功 http://127.0.0.1:8848/nacos/

# liquibase
cd sql-migration
make sku
make agency
make user
make order
make xxl
make management
make logistics
make seata

# seata
cd seata
mvn -Prelease-seata -Dmaven.test.skip=true clean install -U 
cd distribution/target/seata-server-2.0.0/seata/bin/
sh seata-server.sh -h 127.0.0.1 -p 8091 -m db
## 建立後登入後台查看是否成功 http://localhost:7091/ 帳號seata 密碼seata

# xxl
cd xxl
mvn clean install
cd xxl-job-admin
mvn package
docker build --no-cache . -t xxl-job-admin:2.4.0
docker compose up -d
## 建立後登入後台查看是否成功 http://localhost:8056/xxl-job-admin/ 帳號admin 密碼123456

```

3. 建立所有module
```sh
git clone --recurse-submodules <url> # 連同submodule一起clone

cd <module>
mvn clean generate-sources # 生成程式碼

mvn spring-boot:run -Dspring-boot.run.arguments="--spring.config.additional-location=src/main/resources/application-local.yml --spring.profiles.active=local"
```

## 備註

這是 Josh 2026年的 side project，時間短暫許多功能未能完成
