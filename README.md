# Core-BEE 架構說明

## 🧭 NBEE 概述

NBEE 的理念是（Next-Gen Backoffice Enterprise Engine）：用來架構企業用的後台系統。

- NBEE 組成：**Core-BEE** 提供擴充點與介面邊界，**Packages** 依介面約定提供並掛載能力。
- Core-BEE 角色：定義標準化插槽與介面約定（API／事件／型別），負責模組與能力的掛載、解析與生命週期管理。
- Packages 角色：以 Provider 形式實作介面約定，於 Modules 層（例如 `/app/modules`）掛載至相應擴充點（如：`Auth`、`Mailer`、`Storage`、`Queue`、`Payments`、`Search`）。
- Core-BEE 是 NBEE 的擴充點層（Slot Layer）。NBEE = Core-BEE + Packages（以標準介面為邊界的可插拔能力）。
- 使用方式：專案只需選擇合適的 Packages 即可在 Core-BEE 提供的擴充點上組裝能力，達到按需組裝與快速替換。
- 版本與相容：以語義化版本管理介面與實作，保證擴充點與 Packages 的向後相容與漸進式演進。


## 🌐 核心理念

**Core-BEE (Next Backoffice Environment)** 的核心理念聚焦於「可組裝的模組化後端」，以低耦合的介面約定作為連接機制，支撐跨專案的快速復用與演進。

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
- 模組目錄規範：server-actions／rsc／client／i18n／components／tests 標準化
- 依賴聲明與版本管理：使用包管理與語義化版本，明確 peer／dev／prod 依賴
- 契約測試（Contract Testing）與 Mock：以契約測試驗證公開 API；提供 Mock 以支援跨模組並行開發
- CI/CD 流程：在合併前自動跑單元／整合測試與類型檢查，保證品質
- 遷移流程：提供 checklist 與腳本，支援模組在專案間快速搬遷

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

## ⚖️ 優缺點分析

### 優點
- 高可移植性，遷移成本低  
- 結構清晰，易於維護與擴展  
- 模組內測試完善，方便驗證功能  
- UI 與邏輯解耦，提升渲染效能  
- 國際化內建支援

### 潛在挑戰
- 初期架構設計成本較高  
- 團隊需遵守統一模組規範  
- 跨模組溝通需依賴明確 API 文件

---

## 🧭 適用場景

- 企業級管理後台（Admin Portal）  
- 模組化 B2B / SaaS 系統  
- 多國語系應用（i18n）  
- 快速孵化與複用型產品架構  

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
