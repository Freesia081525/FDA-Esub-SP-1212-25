醫療器材查驗登記電子化送件系統導入智慧化審查之可行性方案 (Comprehensive Technical Specification)
Ver: V2.0-Detailed
Date: 2024-05-25
Author: TFDA 智慧審查專案辦公室 / 系統架構師團隊
1. 執行摘要 (Executive Summary)
1.1 策略願景
隨著醫療器材科技日新月異，食品藥物管理署 (TFDA) 面臨查驗登記案件量逐年攀升與技術複雜度增加的雙重挑戰。本計畫旨在透過 人工智慧 (AI) 與 自然語言處理 (NLP) 技術，將現行「電子化送件系統」升級為「智慧審查輔助平台」。

根據 2024 年第一季的使用者調查（N=41），雖然電子化送件在資料存取便利性上獲得肯定 (4.05/5)，但 審查效率 仍受限於海量非結構化文件的檢索與比對。本方案將導入 RAG (Retrieval-Augmented Generation) 架構，預期可縮短 30% 初審時間，並提升技術文件審查的一致性。

1.2 專案目標
短期目標 (6個月)：完成特定類別（如軟體醫材 SaMD）的智慧摘要與語義搜尋 POC，驗證 AI 對醫療法規文本的理解能力。
中期目標 (12個月)：建立 跨文件自動稽核 機制，自動比對申請書、IFU 與測試報告的規格一致性，錯誤檢出率達 90% 以上。
長期目標 (24個月)：實現 全生命週期智慧管理，從送件前預審到上市後監督 (PMS) 的數據貫通。
1.3 關鍵成效指標 (KPIs)
檢索時間：從平均 15 分鐘/案降至 30 秒/案。
摘要準確度：ROUGE-L 分數達 0.8 以上。
系統可用性：尖峰時段 API 回應時間 < 3 秒 (P95)。
2. 現況分析與痛點深掘 (Context & Pain Points)
2.1 使用者旅程痛點分析
透過深度訪談與問卷分析，我們將審查流程中的痛點對應至具體技術缺口：

審查階段
使用者痛點 (Pain Points)
技術缺口 (Technical Gap)
影響評估
案件分派與初審
需逐一打開 PDF 確認產品分類，耗時且易遺漏關鍵屬性。
缺乏 自動分類 (Auto-Classification) 與關鍵字提取能力。
行政效率低落，案件積壓。
實質審查 (技術文件)
臨床前測試報告動輒數百頁，難以快速定位「失敗 (Failed)」或「偏差 (Deviated)」的測試結果。
缺乏 語義理解 (Semantic Understanding) 與異常值偵測機制。
審查品質依賴個人經驗，存在漏看風險。
一致性確認
產品說明書 (IFU) 與測試報告中的規格數據（如滅菌參數、電氣規格）常有出入，需人工來回比對。
缺乏 跨文件實體鏈接 (Cross-Document Entity Linking) 能力。
增加補件往返次數，延宕上市時間。
歷史案件參照
難以快速調閱過去類似案件的決策依據與核准規格，導致審查標準不一。
缺乏 向量化知識庫 (Vectorized Knowledge Base)。
審查一致性不足，廠商抱怨標準浮動。
2.2 系統效能瓶頸
現有系統架構在面對大檔案 (100MB+) 下載與預覽時，常出現 Timeout。調查顯示 85% 使用者曾在尖峰時段遭遇卡頓。這主要源於：

同步處理機制：檔案上傳與病毒掃描佔用 Web Server 執行緒。
資料庫索引不足：傳統關聯式資料庫難以支撐全文檢索需求。
3. 系統架構設計 (System Architecture)
本計畫將採用 混合雲微服務架構 (Hybrid Cloud Microservices)，以平衡資安合規與 AI 運算需求。

3.1 邏輯架構視圖
展示層 (Presentation Layer):
* Smart Review Portal: 基於 React.js 開發的 SPA，整合 PDF Viewer 與 AI 側邊欄。

* Admin Console: 模型監控與系統設定介面。

API 閘道層 (API Gateway):
* 負責請求路由、Rate Limiting、OAuth2 驗證 與 API 稽核。

* 使用 Kong 或 Azure API Management。

業務服務層 (Business Services):
* Submission Service: 處理送件資料 CRUD。

* Workflow Engine: 基於 Camunda 或 Temporal 處理審查流程狀態機。

* Notification Service: 處理 Email 與站內通知。

智慧服務層 (Intelligence Services):
* Ingestion Pipeline: 負責檔案的 OCR、切塊 (Chunking)、Embedding。

* Inference Service: 封裝 LLM 呼叫，實作 Prompt Template 管理與 Context Window 優化。

* Verification Service: 執行一致性比對邏輯。

資料持久層 (Data Layer):
* Object Storage (S3/MinIO): 儲存原始 PDF 與解析後的 JSON。

* Vector Database (<span style="color: coral">Qdrant/Milvus</span>): 儲存文件 Embedding 向量。

* Relational DB (PostgreSQL): 儲存結構化案件資料與使用者權限。

3.2 關鍵技術決策
OCR 引擎：採用 Azure AI Document Intelligence 或開源 PaddleOCR (需自行微調醫療字彙)，以處理掃描檔與表格識別。
向量模型：使用支援多語言長文本的 text-embedding-3-large 或 BGE-M3。
非同步佇列：使用 RabbitMQ 或 Kafka 解耦檔案上傳與 AI 分析流程，解決尖峰效能問題。
3.3 混合雲資料流
1. 資料落地：所有原始送件資料儲存於 TFDA 本地機房或政府私有雲 (Gov Cloud)。

2. 去識別化：在本地端透過 NLP 模型 (如 BERT-NER) 遮罩 PII (病患姓名、身分證號)。

3. 雲端運算：僅將去識別化後的文本特徵送往 Azure OpenAI 進行推理。

4. 結果回傳：推理結果回傳至本地端，與原始資料重新映射 (Re-identification) 後呈現給審查員。

4. AI 核心技術方案 (AI Core Technology)
4.1 RAG (Retrieval-Augmented Generation) 管道設計
為解決 LLM 的幻覺 (Hallucination) 問題，本系統嚴格遵循 RAG 架構。

資料前處理 (Indexing Phase):
* Parsing: 解析 PDF 結構，保留標題層級 (H1-H3) 與表格關聯。

* Chunking: 採用 Semantic Chunking 策略，而非固定字數切割，確保語意完整性。每個 Chunk 約 500-1000 tokens。

* Metadata Tagging: 自動標註文件類型（如：測試報告、證書）、日期、版本號，以利後續過濾 (Pre-filtering)。

檢索階段 (Retrieval Phase):
* Hybrid Search: 結合 關鍵字搜尋 (BM25) 與 向量搜尋 (Dense Vector)，並透過 Reciprocal Rank Fusion (RRF) 演算法重新排序。

* Re-ranking: 使用 Cross-Encoder 模型 (如 BGE-Reranker) 對初步檢索結果進行精細排序，提升相關性。

生成階段 (Generation Phase):
* Context Assembly: 將高相關性 Chunks 組裝至 Prompt。

* Citation Injection: 要求模型在生成的每一句話後標註來源 Chunk ID，以便前端介面實作「點擊溯源」功能。

4.2 模型選型矩陣
評估維度
Azure OpenAI (GPT-4o)
Local LLM (Llama 3 70B)
Taiwan-LLM
邏輯推理能力
極高 (適合複雜比對)
高 (需微調)
中 (適合在地文化內容)
醫療專業知識
高 (預訓練資料豐富)
中
中
資料隱私
中 (企業級合規)
極高 (完全離線)
極高 (完全離線)
維護成本
低 (API 呼叫)
高 (需 GPU 叢集維運)
高
回應速度
取決於網路與 TPM
取決於硬體算力
取決於硬體算力
決策建議：採雙軌制。一般行政文件與公開技術資料使用 Azure OpenAI 以獲取最佳效果；高度機密或涉及個資之文件使用 Local LLM 處理。

4.3 提示詞工程 (Prompt Engineering) 策略
Role Prompting: "You are a senior reviewer at TFDA..."
Chain-of-Thought (CoT): 引導模型逐步推理（例如：「首先識別產品宣稱的效能，其次找出測試報告中的數據，最後比對兩者是否一致」）。
Few-Shot Learning: 在 Prompt 中提供 3-5 個高品質的歷史審查範例（包含正確的摘要與缺失態樣）。
5. 軟體需求規格 (SRS)
5.1 功能性需求 (Functional Requirements)
#### FR-01: 智慧文件摘要 (Smart Summarization)

描述：系統應能針對「臨床前測試報告」生成結構化摘要。
輸入：PDF/Word 格式報告。
輸出：包含「測試目的」、「測試標準 (Standards)」、「測試方法」、「關鍵結果數據」、「結論 (Pass/Fail)」的 Markdown 表格。
驗收標準：對於長度 > 50 頁的報告，摘要生成時間 < 2 分鐘，且準確擷取測試標準編號 (如 ISO 10993-5)。
#### FR-02: 跨文件一致性稽核 (Cross-Check)

描述：自動比對申請書、IFU 與測試報告。
檢查點：產品名稱、型號、滅菌方式、保存期限、適應症。
輸出：不一致報告 (Discrepancy Report)，列出欄位名稱、文件 A 之數值、文件 B 之數值、差異程度。
#### FR-03: 語義問答 (Q&A Chatbot)

描述：使用者可針對特定案件進行對話式查詢。
功能：支援多輪對話，並具備「引用來源」功能，點擊引用可直接跳轉至 PDF 預覽器的對應頁面並高亮文字。
#### FR-04: 同類產品比對 (Peer Comparison)

描述：輸入對照器材 (Predicate Device) 許可證號，自動調閱歷史資料庫。
輸出：生成 實質等效性比對表 (SE Table)，並標註兩者差異處。
5.2 非功能性需求 (Non-Functional Requirements)
NFR-01 回應時間：一般查詢 < 3 秒；複雜推理 < 30 秒。
NFR-02 併發量：支援至少 100 位審查員同時在線操作。
NFR-03 可用性：系統可用性達 99.9% (每月停機時間 < 43 分鐘)。
NFR-04 擴充性：向量資料庫需支援千萬級 Vectors 檢索，回應時間維持亞秒級 (Sub-second)。
6. 安全性與合規性 (Security & Compliance)
6.1 零信任架構 (Zero Trust Architecture)
身分鑑別：全面整合 TFDA 現有之 AD/LDAP，並強制啟用 MFA (多因子驗證)。
設備驗證：僅允許受列管之公務設備 (MDM) 存取後台 API。
最小權限原則 (Least Privilege)：依據職務 (Role) 設定資料存取權限，API Key 不落地，統一由 Key Vault 管理。
6.2 資料隱私保護 (Data Privacy)
PII 去識別化演算法：
* 使用 NER (Named Entity Recognition) 識別姓名、電話、身分證號。

* 採用 Format-Preserving Encryption (FPE) 或遮罩 (Masking) 技術處理敏感字串。

* 例外管理：針對特定法律需求（如廠商名稱需保留以進行利益衝突迴避檢查），設定白名單。

資料加密：
* 傳輸中 (In-Transit)：TLS 1.3。

* 靜態 (At-Rest)：AES-256 加密儲存，金鑰由 HSM (Hardware Security Module) 管理。

6.3 稽核與監控 (Audit & Monitoring)
全路徑日誌：記錄每一次 AI 呼叫的 Prompt、Token Usage、Response 以及使用者 ID。
異常偵測：針對異常大量的資料下載或高頻率的 AI 查詢行為，設置自動告警 (Alerting)。
模型監控：定期評估模型輸出的品質，偵測是否出現 Model Drift 或有害內容。
7. 基礎設施與部署 (Infrastructure & Deployment)
7.1 Kubernetes 叢集規劃
建議採用 GKE (Google Kubernetes Engine) 或 AKS (Azure Kubernetes Service) 搭配地端節點 (Anthos/Arc)。

Node Pools:
* System Pool: 運行 API Gateway, Ingress, Monitoring Agent。

* App Pool: 運行 Web UI, Backend API (Auto-scaling enabled)。

* AI Pool: 配備 GPU (NVIDIA T4/L4) 運行開源 OCR 模型與 Embedding 模型。

* Vector DB Pool: 高記憶體配置 (High-Memory) 運行 Qdrant/Milvus。

7.2 基礎設施即程式碼 (IaC) - Terraform 範例
```hcl

# 定義 Azure Kubernetes Service

resource "azurerm_kubernetes_cluster" "tfda_aks" {

name = "tfda-aks-prod"

location = azurerm_resource_group.rg.location

resource_group_name = azurerm_resource_group.rg.name

dns_prefix = "tfda-aks"

default_node_pool {

name = "default"

node_count = 3

vm_size = "Standard_D4s_v5" # General Purpose

}

# GPU Node Pool for AI Inference

node_pool {

name = "gpunodes"

vm_size = "Standard_NC4as_T4_v3" # T4 GPU

node_count = 2

mode = "User"

taints = ["sku=gpu:NoSchedule"]

}

identity {

type = "SystemAssigned"

}

}

```

7.3 硬體資源估算 (地端部署選項)
若需完全地端部署 Taiwan-LLM (70B)，建議硬體規格：

GPU Server: 2x NVIDIA A100 (80GB) 或 4x NVIDIA A6000 Ada。
CPU: Dual AMD EPYC (64 cores)。
RAM: 512GB DDR5。
Storage: 10TB NVMe SSD (RAID 10) 供向量庫與原始檔存取。
8. 驗證與確效 (Validation & Verification)
本系統雖非醫療器材，但作為監管決策輔助工具，建議參照 GAMP 5 (Good Automated Manufacturing Practice) 與 CSV (Computer System Validation) 精神進行確效。

8.1 V-Model 驗證流程
使用者需求 (URS) ↔ 性能確認 (PQ): 確認系統在真實審查場景下能提升效率。
功能規格 (FS) ↔ 操作確認 (OQ): 測試各功能模組（如摘要、搜尋）是否符合預期輸出。
設計規格 (DS) ↔ 安裝確認 (IQ): 確認系統正確部署於指定環境且配置無誤。
8.2 AI 特定測試 (AI-Specific Testing)
Ground Truth Evaluation: 建立包含 100 份歷史案件的「黃金標準測試集」，由資深審查員標註正確摘要與比對結果。
Hallucination Rate: 透過自動化腳本檢查 AI 生成內容中的數據是否真實存在於原始文件中（Fact-checking）。
Robustness Test: 測試掃描品質不佳、歪斜、含有浮水印的 PDF，確認 OCR 與 AI 的容錯能力。
Adversarial Attack: 測試惡意 Prompt 是否能繞過安全機制導出敏感資料。
8.3 持續監控 (Continuous Monitoring)
建立 Human-in-the-loop (HITL) 回饋機制：審查員可對 AI 輸出按讚/倒讚或修正。這些回饋數據將儲存於 Data Lake，用於下一版本的模型微調 (Fine-tuning)。

9. 專案管理與實施時程 (Project Management)
Phase 1: 基礎建設與 POC (Month 1-4)
Month 1: 需求訪談、架構設計確認、採購雲端資源。
Month 2: 建立 RAG 基礎管道 (OCR + Vector DB)，完成資料清洗。
Month 3: 開發「智慧語義搜尋」原型，邀請 5 位核心使用者測試。
Month 4: POC 結案報告，評估技術可行性與修正方向。
Phase 2: 核心功能開發 (Month 5-10)
Month 5-6: 開發「智慧摘要」與「一致性稽核」模組，整合 LLM API。
Month 7: 前端介面整合 (React UI)，開發側邊欄助手功能。
Month 8: 系統整合測試 (SIT)，串接身分驗證與現有資料庫。
Month 9: 使用者驗收測試 (UAT)，擴大至 20 位使用者。
Month 10: 資安弱點掃描與滲透測試，修正高風險漏洞。
Phase 3: 全面上線與優化 (Month 11-12+)
Month 11: 正式環境部署 (Production Deployment)，教育訓練。
Month 12: 全面切換上線 (Go-live)，啟動維運監控。
On-going: 每季依據使用者回饋與新 AI 模型技術進行迭代更新。
10. 效益分析 (Cost-Benefit Analysis)
10.1 質化效益
提升審查一致性：減少因人為疏忽或經驗落差導致的審查標準不一。
知識傳承：將資深審查員的隱性知識透過 Prompt Engineering 轉化為系統邏輯。
優化廠商體驗：減少因格式錯誤或簡單缺失導致的補件往返，加速產品上市。
10.2 量化效益 (ROI 估算)
假設 TFDA 每年受理 3,000 件醫材申請案，平均每案審查工時 40 小時。

初審效率提升：智慧分類與檢索節省 30% 時間 → 3,000 * 40 * 30% = 36,000 小時/年。
技術審查效率提升：智慧摘要與比對節省 20% 時間 → 3,000 * 40 * 20% = 24,000 小時/年。
總節省工時：約 60,000 小時/年。
若以專家審查員時薪 NT$ 1,000 計算，每年可創造 NT$ 6,000 萬 的時間價值。

扣除系統建置與維運成本 (預估首年 2,000 萬，次年起 500 萬)，ROI 預計在 第 6 個月 即可轉正。

11. 風險管理 (Risk Management)
風險項目
發生機率
影響程度
緩解措施 (Mitigation Plan)
AI 幻覺 (Hallucination)
中
高
1. 介面強制顯示引用來源 (Citations)。<br>2. 提示詞加入 "若資訊不足請回答不知道" 約束。<br>3. 關鍵數據需人工二次確認。
資料外洩 (Data Leakage)
低
極高
1. 採用 Azure OpenAI 企業版 (Zero Data Retention)。<br>2. 嚴格執行 PII 去識別化。<br>3. 定期資安稽核。
系統整合複雜度高
高
中
1. 採用微服務架構，逐步替換舊模組。<br>2. 保持 API 介面相容性。<br>3. 建立詳細的 Swagger 文件。
使用者抗拒改變
中
中
1. 早期邀請意見領袖 (KOL) 參與設計。<br>2. 設計直覺的 UI，降低學習門檻。<br>3. 強調 AI 為「助手」而非「取代者」。
運算成本超支
中
低
1. 實作 Token 使用量監控與 Quota 限制。<br>2. 優化 Prompt 長度。<br>3. 善用 Cache 機制減少重複推理。
12. 附錄：20 個綜合追蹤問題 (20 Follow-up Questions)
為確保本方案能順利落地並持續優化，建議專案團隊在後續規劃中深入探討以下問題：

1. AI 治理：TFDA 是否需要成立專門的 AI 倫理委員會來審核模型的決策邏輯？

2. 法規調適：現行法規是否允許審查員引用 AI 生成的摘要作為正式審查意見的一部分？

3. 模型更新：當 Azure OpenAI 發布新模型 (如 GPT-5) 時，系統的升級與驗證 SOP 為何？

4. 多語言支援：系統對於非英文/中文（如日文、德文原廠報告）的處理能力與翻譯準確度如何驗證？

5. 廠商端應用：未來是否考慮開放部分 AI 預審功能給廠商，讓其在送件前自行檢查缺失？

6. 地端算力：若政策強制要求完全離線，TFDA 機房的電力與空調設施是否足以支撐高密度 GPU 伺服器？

7. 人才培訓：如何培訓現有審查員具備基本的 Prompt Engineering 技巧，以更精準地指揮 AI？

8. 錯誤責任：若因 AI 摘要錯誤導致審查誤判（如漏看嚴重副作用），責任歸屬如何界定？

9. 資料標註：如何建立持續性的回饋機制，讓審查員的修正能有效回流至模型微調？

10. 版權問題：RAG 資料庫中儲存的 ISO 標準、學術期刊是否涉及版權授權問題？

11. 災難復原：若 Azure 區域性服務中斷，系統是否有降級 (Degrade) 運作模式或備援機制？

12. API 經濟：是否考慮將去識別化後的審查數據透過 API 開放給學研單位進行監管科學研究？

13. 跨部會整合：本系統是否能與健保署或海關系統介接，實現醫材全生命週期管理？

14. 使用者介面：針對習慣紙本作業的資深審查員，UI 設計是否有足夠的友善度與過渡輔助？

15. 效能優化：針對數千頁的超大型 PDF，Chunking 與 Embedding 的處理策略是否會造成過長延遲？

16. 測試資料：在開發階段如何取得足夠多樣且合規的真實案件數據進行模型測試？

17. 預算編列：長期的雲端 Token 費用與 GPU 維護費用應編列於資本門還是經常門？

18. 供應商鎖定：若未來需更換 AI 模型供應商 (如從 OpenAI 轉至 Google Gemini)，架構的抽換成本為何？

19. 資安邊界：在地端與雲端進行資料交換時，如何確保 VPN 通道的絕對安全與監控？

20. 國際接軌：本系統的資料標準是否符合 IMDRF (國際醫療器材監管機構論壇) 的規範，以利未來國際合作？
