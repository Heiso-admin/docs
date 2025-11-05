# Core-BEE 架構說明

## NBEE 概述

NBEE（Next‑Gen Base Enterprise Environment）用於構建企業中台。

- 組成：Core‑BEE（擴充點與介面邊界）＋ 套件（依約定提供能力）
- 用法：挑選所需能力並掛載到擴充點，按需組裝、快速替換
- 版本：採語義化版本，維持向後相容與漸進演進

## 核心理念

核心理念：以低耦合介面約定串接模組，支撐跨專案復用與演進。

核心原則：
- 邊界清楚：僅以公開 API／事件／資料模型互動；禁止隱式耦合與共享狀態。
- 易移植、易組裝：避免框架鎖定與全域依賴；跨專案低成本復用。
- 環境一致：標準化工具、配置與部署；行為可預期。
- 擴充點架構：Core‑BEE 提供擴充點與介面約定；套件可熱替換。

設計目標：
- 降成本、提復用
- 快迭代、易替換
- 邊界清晰、版本穩定

設計約束：
- 分層解耦；僅向下依賴
- 介面先行、文件自動化（OpenAPI）
- 漸進變更：棄用→替代→移除，保留過渡期

## 目錄
- [概述](#nbee-概述)
- [核心理念](#核心理念)
- [快速開始](#快速開始)
- [功能](#功能)
- [技術棧](#技術棧)
- [指令](#指令)
- [Dev Center](#dev-center)
- [授權](#授權)

## 快速開始
- 取得專案與安裝依賴
```
git clone https://github.com/Heiso-admin/Core-BEE.git
cd Core-BEE
pnpm install
```

- 設定環境與啟動
```
# .env.local 需包含 DATABASE_URL 與 NEXTAUTH_SECRET
pnpm db:push
pnpm dev
```

- 首次註冊：造訪 http://localhost:3000/signup 建立帳號
- 注意：註冊後於 `developers` 資料表加入自己的帳號以存取 `/dev-center`

## 功能
- NextAuth v5，支援雙重驗證（2FA）
- 以資源為中心的 RBAC 權限
- Plate.js 編輯器與 AI
- PostgreSQL 與 Drizzle ORM
- 多語系（繁體中文、英文）
- S3 檔案上傳
- Radix、Tailwind v4、Shadcn UI
- React Email 與 Resend
- Dev Center（開發者中台）：管理 Developers、Menu、Permission、API Keys、API Docs、Keys

## 技術棧
- 核心：Next.js 16、React 19、TypeScript 5
- 資料庫：PostgreSQL、Drizzle ORM
- 驗證：NextAuth 5.0
- 介面：Tailwind v4、Radix UI、Shadcn UI
- 編輯器：Plate.js 49、AI SDK
- 工具：Biome、pnpm、dotenv-flow

## 指令
```
pnpm dev          # 啟動開發伺服器
pnpm build        # 產生正式版建置
pnpm db:push      # 套用資料庫綱要
pnpm db:studio    # 開啟資料庫介面
pnpm lint         # 稽核程式碼
pnpm format       # 格式化程式碼
```



## Dev Center

- 入口：`/dev-center`。首次註冊後，於 `developers` 資料表加入自己的帳號即可存取。
- 功能項：
  - Team：以團隊為單位管理成員與資源邊界。
  - Role：可重用的權限模板（資源／操作），可指派給團隊或成員。
  - O365：與 Microsoft 365 整合，支援 SSO 與使用者／群組同步。
  - Developers：管理開發者名單、角色與權限綁定。
  - Menu：維護系統菜單結構與可見性控制。
  - Permission：以資源為中心的權限模型與授權規則（操作／範圍）。
  - API Keys：發行、輪替、停用 API Key；設定 Scope 與有效期。
  - API Docs：瀏覽與測試 API；基於 OpenAPI 自動生成。
  - Keys：集中管理應用密鑰與第三方服務憑證。

### 工程師 Tool Kit

目標：讓工程師不必處理基礎設施設定，只專注在模組內的 business logic。

- API Keys：一鍵發行／輪替／停用，設定 Scope 與有效期。
- API Docs：即看即測（OpenAPI 驅動），提供 curl／snippet 便於整合。
- Permission：以資源為中心設計權限，建立角色模板並套用到開發者。
- Keys：集中管理應用密鑰與第三方憑證，支援標記與審計。
- Developers：建立／停用開發者、綁定角色與權限，維持協作邊界。
- Menu：編輯管理後台菜單與可見性，快速對齊產品導覽。
 - Team：以團隊維度管理成員、環境與資源邊界。
 - Role：建立可重用的權限模板，快速指派到團隊或成員。
 - O365：與 Microsoft 365（Azure AD/Entra ID）整合；支援 SSO 與使用者／群組同步、角色映射。

快速流程示例：
- 定義資源與操作 → 建立角色模板 → 發行 API Key（設定 Scope）→ 在 API Docs 驗證呼叫 → 交付模組業務邏輯。

#### API Keys Management

- 入口與動作：在 Dev Center 的「API Keys」頁，點擊 `Create API Key` 建立金鑰。
- 欄位與策略：
  - 名稱（可辨識用途）
  - Scope（可存取的資源／操作）
  - Expiration（有效期，支援永久或日期區間）
  - Rotation（輪替策略，建議定期輪替）
- 常見操作：
  - Create：建立可用金鑰，發放給服務或外部整合。
  - Rotate：停用舊金鑰、生成新金鑰，不中斷服務。
  - Revoke：立即撤銷；用於外洩或結束合作。
- 呼叫方式：於請求加上 `Authorization: Bearer <api_key>`，並確保 Scope 符合端點需求。
- 安全提示：金鑰只在建立時顯示一次；請保存於安全儲存（如 Secrets Manager），避免硬編碼到程式碼庫。

落地實踐：
- 目錄：server-actions／rsc／client／i18n／components／tests
- 依賴與版本：語義化版本；明確 peer／dev／prod
- 測試：契約測試（Contract Testing）＋ Mock（驗證公開 API，支援並行開發）
- CI/CD：合併前跑單元／整合測試與型別檢查
- 遷移：提供 checklist／腳本，跨專案快速搬遷

---

## 名詞對齊

- 擴充點（Slot）：Core‑BEE 暴露的掛載位置，用於裝配能力
- 介面約定（Interface Contract）：模組互動的規範（API／事件／型別）
- 提供者（Provider／Package）：依介面約定提供能力並掛載至擴充點

---

## 架構總覽

層級：**App**／**Modules**／**Components**／**Libraries**

---

## 架構分層說明

- App 層：應用入口與整體組織
- Modules 層：業務模組與 API 封裝（支援 Swagger／OpenAPI）
- Components 層：共用 UI 與 Blocks（基於 shadcn/ui）
- Libraries 層：工具庫與第三方整合（ORM、AWS SDK、Utils、Email、i18n）

---

## Module 內部結構

每個模組目錄包含：伺服器動作（Server Actions）／RSC（React 伺服器元件）／用戶端元件（Client Components）／多語言（i18n JSON）／模組元件（Module Components）／測試（Testing）

---

## 模組可移植性

- 自包含依賴；公共依賴由 Libraries 層提供
- API 與 UI 解耦；遷移低成本
- 跨專案復用；更新不影響全局
- 邊界清晰；協作明確

---

## 專案結構範例

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

## 授權
本專案採用 MIT License。
