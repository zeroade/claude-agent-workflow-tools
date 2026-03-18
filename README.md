# Prompt Validation Skill  (claude)
**強制性 Prompt 驗證流程** – Claude Agent Workflow Tools

防止 prompt 改壞、失效、被繞過的標準化驗證流程  
**任何修改或新設計會影響 AI 判斷行為的文字（prompt），都必須先跑這個流程**

## 為什麼需要這個流程？

在大多數 agent 系統中，prompt 一旦進入 main node 或 hook injection 環境，就會面對大量噪音（system instructions、記憶、其他 hook、對話歷史等）。  
很多看起來「很強」的 prompt，在真實噪音下會瞬間崩潰、被繞過、失效或產生幻覺。

這個流程透過**三階段隔離測試** + **最低 10 輪** 的迭代，強迫證明 prompt 在真實環境下仍然有效，而不是只在乾淨 context 裡看起來漂亮。

## 核心原則

- **隔離至上**：challenge agent 絕對不能看到待測 prompt，response agent 絕對不能看到預期答案
- **噪音真實化**：每輪產生全新、不同的噪音塊（1500–2500 字），模擬真實 main node 環境
- **機制驅動而非參數微調**：優化時要換 driving mechanism，而不是只改語氣、加粗、換 emoji
- **可驗證性第一**：所有目標都要能轉成明確、可逐句對應的驗證標準

## 使用流程總覽

```mermaid
graph TD
    A[要改/寫新的 prompt] --> B[Phase 0: 定義目標 & 驗證標準]
    B --> C[Phase 1: 循序迭代直到達標]
    C --> D[Phase 2: 平行探索 & 優化（累計至少 10 輪）]
    D --> E[Phase 3: 輸出報告 + 記錄好/壞 topology]
    D -->|歷史最佳 10 輪仍未達標| F[Phase F: 失敗路徑<br>評估是否目標不切實際]
