# 優質React前端系統架構

> 這是一個採用 **特徵切片設計 (Feature-Sliced Design, FSD)** 或 **領域驅動設計 (Domain-Driven Design, DDD)** 概念，基於 **React/TypeScript** 框架構建的現代前端應用程式架構。
> 
> 這種結構的目標是實現高**內聚性 (Cohesion)** 和低**耦合度 (Coupling)**，從而提升專案的**可擴展性 (Scalability)** 和**可維護性 (Maintainability)**。
> 
---

## 系統架構設計詳細分析

### 1. 架構範式：特徵切片與元件化

此專案主要遵循兩大核心原則：

#### A. 領域/特徵驅動 (Domain/Feature-Driven)
程式碼不是按技術類型（如所有的 `hooks` 放在一起），而是按**業務領域**劃分為獨立的模組。
*   **體現：** 核心業務邏輯位於 `src/features` 資料夾下，並進一步劃分為 `onboarding` 和 `profits` 等獨立的業務領域（即「特徵切片」）。
*   **優勢：** 當需要修改 `profits` 邏輯時，所有相關程式碼都集中在 `src/features/profits` 內，實現了**垂直切片**，減少了跨模組的依賴和潛在的副作用。

#### B. 核心元件化 (Core Componentization)
應用程式的 UI 被分解為可重用的元件。
*   **體現：** 存在一個獨立的 `src/components` 資料夾，通常用於存放不依賴任何特定業務邏輯的**啞元件 (Dumb/Presentational Components)** 或 **設計系統 (Design System)** 元件，例如圖中的 `button.tsx`。

### 2. 分層結構與關注點分離 (Separation of Concerns)

應用程式的程式碼被清晰地劃分為幾個邏輯層次：

| 程式碼層級                                        | 資料夾名稱                                                   | 專業職責與功能                                                                                   |
| :------------------------------------------- | :------------------------------------------------------ | :---------------------------------------------------------------------------------------- |
| **I. 應用入口層 (Entry Layer)**                   | `src/pages`                                             | 負責應用程式的**路由 (Routing)** 結構，每個檔案通常對應一個主要的 URL 視圖。它是將特徵模組組合到頁面上的地方。                         |
| **II. 特徵/領域層 (Feature/Domain Layer)**        | `src/features/{domain}`                                 | 封裝**業務邏輯**和**領域知識**。這是應用程式的核心價值所在。                                                        |
| **III. 邏輯/狀態管理層 (Logic/State Layer)**        | `features/{domain}/hooks`, `features/{domain}/contexts` | 實現**狀態管理**、**非同步資料獲取**和**邏輯抽象**。Custom Hooks 是 React 生態中實現邏輯重用的關鍵手段。Contexts 用於該領域內的狀態共享。 |
| **IV. 基礎設施層 (Infrastructure/Utility Layer)** | `src/utils`, `src/helpers`                              | 存放**跨領域**的通用輔助功能，如日期格式化、通用驗證器、或 API 抽象層。它們不應包含任何業務邏輯。                                     |

### 3. 特徵模組內部結構 (以 `profits` 為例)

`src/features/profits` 體現了一個高內聚的特徵切片：

1.  **`profits/components`**: 存放**智慧元件 (Smart/Container Components)** 或其他僅在 `profits` 模組內部使用的 UI 元件。它們通常包含業務邏輯或狀態連接。
2.  **`profits/contexts`**: 存放該特徵專屬的 **React Contexts**，用於管理複雜的、需要跨元件傳遞的 `profits` 相關狀態。
3.  **`profits/hooks`**: 存放專門用於 `profits` 邏輯的 **Custom Hooks**，例如 `useProfitCalculation` 或 `useProfitChartData`，將複雜的計算邏輯從 UI 元件中抽離。
4.  **`profits/helpers`**: 存放僅供 `profits` 模組內部使用的**輔助工具函數**。

### 4. 程式碼內容分析 (`constants.ts`)

*   **路徑細節：** 檔案位於 `src/features/profits/hooks/constants.ts`。
*   **常量定義：** `const WHITE_USER_USER_IDS = [...]`
*   **專業含義：**
    *   這是一個**領域配置 (Domain Configuration)** 檔案。它將特定於 `profits` 模組的**靜態配置資料**（例如：白名單 ID、功能開關的閾值）與執行邏輯分離。
    *   將其放在 `hooks` 資料夾下，雖然不完全標準 (通常會放在特徵根目錄或專門的 `config` 資料夾)，但這暗示了這些常量是 `profits` 模組中的 Hooks 所**直接依賴**的核心配置數據。
    *   使用 **TypeScript (`.ts`)** 確保了 `WHITE_USER_USER_IDS` 這個陣列的**類型安全 (Type Safety)** 和結構清晰。

### 5. 總結：主要設計優勢

*   **可擴展性 (Scalability):** 新的業務功能（如 `settings` 或 `notifications`）可以直接作為一個新的 `features/settings` 資料夾加入，不影響現有程式碼。
*   **維護性 (Maintainability):** 透過將業務邏輯封裝在特徵切片中，可以極大地降低**認知負擔 (Cognitive Load)**，開發人員能更快地理解和修改特定領域的程式碼。
*   **測試性 (Testability):** 清晰的分層結構使單元測試的範圍更明確。例如，Hooks 邏輯可以獨立於 UI 元件進行測試。
*   **團隊協作 (Team Collaboration):** 不同的團隊可以專注於不同的 `features` 模組而不會發生過多的程式碼衝突。
