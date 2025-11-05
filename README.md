# Core-BEE 架構說明

## 🧭 NBEE 概述

NBEE（Next‑Gen Backoffice Enterprise Engine）用於構建企業後台系統。

- 組成：Core‑BEE（擴充點與介面邊界）＋ Packages（依約定提供能力）
- 用法：挑選所需能力並掛載到擴充點，按需組裝、快速替換
- 版本：採語義化版本，維持向後相容與漸進演進


## 🌐 核心理念

核心理念：以低耦合介面約定串接模組，支撐跨專案復用與演進。

核心原則：
- 邊界清楚：僅以公開 API／事件／資料模型互動；禁止隱式耦合與共享狀態。
- 易移植、易組裝：避免框架鎖定與全域依賴；跨專案低成本復用。
- 環境一致：標準化工具、配置與部署；行為可預期。
- 插槽化架構：Core-BEE 提供擴充點與介面約定；Packages 可熱替換。

設計目標：
- 降低重複開發成本，提升功能復用率
- 加速迭代與替換，縮短從設計到上線的週期
- 強化團隊協作，模組邊界明確，責任切分清楚
- 保持穩定版本演進，透過語義化版本與變更策略保障向後相容

設計約束：
- 分層解耦：App／Modules／Components／Libraries 各司其職，不跨層直接依賴
- 依賴向下不向上：模組僅依賴 Libraries／公共能力；禁止跨模組直接訪問私有實現
- API 先行與文件化：接口定義、錯誤碼、型別約定、示例須完善並可自動生成（如 OpenAPI）
- 變更策略：對外介面採用 deprecate→替代→移除的節奏，保留過渡期與遷移指南

落地實踐：
- 目錄：server-actions／rsc／client／i18n／components／tests
- 依賴與版本：語義化版本；明確 peer／dev／prod
- 測試：契約測試＋Mock（驗證公開 API，支援並行開發）
- CI/CD：合併前跑單元／整合測試與型別檢查
- 遷移：提供 checklist／腳本，跨專案快速搬遷

---

## 🏗 架構總覽

該架構將整個 Next.js 應用分為四個層級：

- **App 層**：應用入口與整體組織  
- **Modules 層**：業務邏輯模組化管理  
- **Components 層**：共用 UI 與功能組件封裝  
- **Libraries 層**：通用工具庫與第三方整合

---

## 🧩 架構分層說明

### App 層
負責整個應用的入口與組織：
- **Modules**：各模組獨立實現業務邏輯，透過 API 或共享庫交互
- **API Modules**：對應後端 API 的封裝與文件（支援 Swagger / OpenAPI）

### Components 層
封裝可重用的 UI 與業務組件：
- **Custom Blocks**：自訂可重用組件  
- **shadcn UI**：基於 shadcn/ui 的可定制基礎組件庫

### Libraries 層
提供通用工具庫與第三方整合：
- **ORM**（Prisma / Drizzle ORM）  
- **AWS SDK 封裝**  
- **Hash / Utils / Email / i18n 工具模組**

---

## 🧱 Module 內部結構

每個模組的目錄結構固定，包含：

- **Server Actions**：伺服器端邏輯（支援 Next.js 15 Server Actions）  
- **RSCs**（React Server Components）：負責 SSR / 靜態生成  
- **Client Components**：交互型組件（運行於瀏覽器端）  
- **I18n Jsons**：多語言資源文件  
- **Module Components**：模組專屬組件  
- **Testing**：單元測試與整合測試

---

## 🚀 模組可移植性設計

### 設計原則
- 模組內依賴盡量自包含，減少全局依賴  
- 公共依賴透過 **Libraries 層** 提供  
- API 與 UI 解耦，遷移時僅需少量調整

### 好處
- **跨專案復用**：模組可直接移植  
- **快速迭代**：功能更新不影響全局  
- **團隊協作清晰**：模組邊界明確

---

## 📁 專案結構範例

```
/app
  /modules
    /user
      /server-actions
      /components
      /client
      /i18n
      /tests
  /components
    /ui
    /blocks
  /libraries
    /orm
    /aws
    /utils
```

---

## 📚 授權
本專案採用 MIT License。
