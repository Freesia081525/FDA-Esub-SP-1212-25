醫療器材審查系統智慧化導入
Last saved Dec 13 at 5:45 AM

Prompt

Evaluate
System Prompt
please think deeper and iterate until get excellent results. please don't ask any other questions. Ending with 20 comprehensive follow up questions.

Drop here to insert into user message
Max 100 files at 5MB each
User
Hi please reorganize the report in traditional chinese in markdown with key  words in coral color to 5000 ~ 8000 words based on the following information:醫療器材查驗登記電子化送件系統導入智慧化審查之可行性方案 (Comprehensive Technical Specification) Ver: V2.0-Detailed Date: 2024-05-25 Author: TFDA 智慧審查專案辦公室 / 系統架構師團隊



執行摘要 (Executive Summary) 1.1 策略願景 隨著醫療器材科技日新月異，食品藥物管理署 (TFDA) 面臨查驗登記案件量逐年攀升與技術複雜度增加的雙重挑戰。本計畫旨在透過 人工智慧 (AI) 與 自然語言處理 (NLP) 技術，將現行「電子化送件系統」升級為「智慧審查輔助平台」。

根據 2024 年第一季的使用者調查（N=41），雖然電子化送件在資料存取便利性上獲得肯定 (4.05/5)，但 審查效率 仍受限於海量非結構化文件的檢索與比對。本方案將導入 RAG (Retrieval-Augmented Generation) 架構，預期可縮短 30% 初審時間，並提升技術文件審查的一致性。



1.2 專案目標 短期目標 (6個月)：完成特定類別（如軟體醫材 SaMD）的智慧摘要與語義搜尋 POC，驗證 AI 對醫療法規文本的理解能力。 中期目標 (12個月)：建立 跨文件自動稽核 機制，自動比對申請書、IFU 與測試報告的規格一致性，錯誤檢出率達 90% 以上。 長期目標 (24個月)：實現 全生命週期智慧管理，從送件前預審到上市後監督 (PMS) 的數據貫通。 1.3 關鍵成效指標 (KPIs) 檢索時間：從平均 15 分鐘/案降至 30 秒/案。 摘要準確度：ROUGE-L 分數達 0.8 以上。 系統可用性：尖峰時段 API 回應時間 < 3 秒 (P95)。 2. 現況分析與痛點深掘 (Context & Pain Points) 2.1 使用者旅程痛點分析 透過深度訪談與問卷分析，我們將審查流程中的痛點對應至具體技術缺口：



審查階段 使用者痛點 (Pain Points) 技術缺口 (Technical Gap) 影響評估 案件分派與初審 需逐一打開 PDF 確認產品分類，耗時且易遺漏關鍵屬性。 缺乏 自動分類 (Auto-Classification) 與關鍵字提取能力。 行政效率低落，案件積壓。 實質審查 (技術文件) 臨床前測試報告動輒數百頁，難以快速定位「失敗 (Failed)」或「偏差 (Deviated)」的測試結果。 缺乏 語義理解 (Semantic Understanding) 與異常值偵測機制。 審查品質依賴個人經驗，存在漏看風險。 一致性確認 產品說明書 (IFU) 與測試報告中的規格數據（如滅菌參數、電氣規格）常有出入，需人工來回比對。 缺乏 跨文件實體鏈接 (Cross-Document Entity Linking) 能力。 增加補件往返次數，延宕上市時間。 歷史案件參照 難以快速調閱過去類似案件的決策依據與核准規格，導致審查標準不一。 缺乏 向量化知識庫 (Vectorized Knowledge Base)。 審查一致性不足，廠商抱怨標準浮動。 2.2 系統效能瓶頸 現有系統架構在面對大檔案 (100MB+) 下載與預覽時，常出現 Timeout。調查顯示 85% 使用者曾在尖峰時段遭遇卡頓。這主要源於：



同步處理機制：檔案上傳與病毒掃描佔用 Web Server 執行緒。 資料庫索引不足：傳統關聯式資料庫難以支撐全文檢索需求。 3. 系統架構設計 (System Architecture) 本計畫將採用 混合雲微服務架構 (Hybrid Cloud Microservices)，以平衡資安合規與 AI 運算需求。



3.1 邏輯架構視圖 展示層 (Presentation Layer):



Smart Review Portal: 基於 React.js 開發的 SPA，整合 PDF Viewer 與 AI 側邊欄。



Admin Console: 模型監控與系統設定介面。



API 閘道層 (API Gateway):



負責請求路由、Rate Limiting、OAuth2 驗證 與 API 稽核。



使用 Kong 或 Azure API Management。



業務服務層 (Business Services):



Submission Service: 處理送件資料 CRUD。



Workflow Engine: 基於 Camunda 或 Temporal 處理審查流程狀態機。



Notification Service: 處理 Email 與站內通知。



智慧服務層 (Intelligence Services):



Ingestion Pipeline: 負責檔案的 OCR、切塊 (Chunking)、Embedding。



Inference Service: 封裝 LLM 呼叫，實作 Prompt Template 管理與 Context Window 優化。



Verification Service: 執行一致性比對邏輯。



資料持久層 (Data Layer):



Object Storage (S3/MinIO): 儲存原始 PDF 與解析後的 JSON。



Vector Database (Qdrant/Milvus): 儲存文件 Embedding 向量。



Relational DB (PostgreSQL): 儲存結構化案件資料與使用者權限。



3.2 關鍵技術決策 OCR 引擎：採用 Azure AI Document Intelligence 或開源 PaddleOCR (需自行微調醫療字彙)，以處理掃描檔與表格識別。 向量模型：使用支援多語言長文本的 text-embedding-3-large 或 BGE-M3。 非同步佇列：使用 RabbitMQ 或 Kafka 解耦檔案上傳與 AI 分析流程，解決尖峰效能問題。 3.3 混合雲資料流



資料落地：所有原始送件資料儲存於 TFDA 本地機房或政府私有雲 (Gov Cloud)。



去識別化：在本地端透過 NLP 模型 (如 BERT-NER) 遮罩 PII (病患姓名、身分證號)。



雲端運算：僅將去識別化後的文本特徵送往 Azure OpenAI 進行推理。



結果回傳：推理結果回傳至本地端，與原始資料重新映射 (Re-identification) 後呈現給審查員。



AI 核心技術方案 (AI Core Technology) 4.1 RAG (Retrieval-Augmented Generation) 管道設計 為解決 LLM 的幻覺 (Hallucination) 問題，本系統嚴格遵循 RAG 架構。



資料前處理 (Indexing Phase):



Parsing: 解析 PDF 結構，保留標題層級 (H1-H3) 與表格關聯。



Chunking: 採用 Semantic Chunking 策略，而非固定字數切割，確保語意完整性。每個 Chunk 約 500-1000 tokens。



Metadata Tagging: 自動標註文件類型（如：測試報告、證書）、日期、版本號，以利後續過濾 (Pre-filtering)。



檢索階段 (Retrieval Phase):



Hybrid Search: 結合 關鍵字搜尋 (BM25) 與 向量搜尋 (Dense Vector)，並透過 Reciprocal Rank Fusion (RRF) 演算法重新排序。



Re-ranking: 使用 Cross-Encoder 模型 (如 BGE-Reranker) 對初步檢索結果進行精細排序，提升相關性。



生成階段 (Generation Phase):



Context Assembly: 將高相關性 Chunks 組裝至 Prompt。



Citation Injection: 要求模型在生成的每一句話後標註來源 Chunk ID，以便前端介面實作「點擊溯源」功能。



4.2 模型選型矩陣 評估維度 Azure OpenAI (GPT-4o) Local LLM (Llama 3 70B) Taiwan-LLM 邏輯推理能力 極高 (適合複雜比對) 高 (需微調) 中 (適合在地文化內容) 醫療專業知識 高 (預訓練資料豐富) 中 中 資料隱私 中 (企業級合規) 極高 (完全離線) 極高 (完全離線) 維護成本 低 (API 呼叫) 高 (需 GPU 叢集維運) 高 回應速度 取決於網路與 TPM 取決於硬體算力 取決於硬體算力 決策建議：採雙軌制。一般行政文件與公開技術資料使用 Azure OpenAI 以獲取最佳效果；高度機密或涉及個資之文件使用 Local LLM 處理。



4.3 提示詞工程 (Prompt Engineering) 策略 Role Prompting: "You are a senior reviewer at TFDA..." Chain-of-Thought (CoT): 引導模型逐步推理（例如：「首先識別產品宣稱的效能，其次找出測試報告中的數據，最後比對兩者是否一致」）。 Few-Shot Learning: 在 Prompt 中提供 3-5 個高品質的歷史審查範例（包含正確的摘要與缺失態樣）。 5. 軟體需求規格 (SRS) 5.1 功能性需求 (Functional Requirements)



FR-01: 智慧文件摘要 (Smart Summarization)

描述：系統應能針對「臨床前測試報告」生成結構化摘要。 輸入：PDF/Word 格式報告。 輸出：包含「測試目的」、「測試標準 (Standards)」、「測試方法」、「關鍵結果數據」、「結論 (Pass/Fail)」的 Markdown 表格。 驗收標準：對於長度 > 50 頁的報告，摘要生成時間 < 2 分鐘，且準確擷取測試標準編號 (如 ISO 10993-5)。



FR-02: 跨文件一致性稽核 (Cross-Check)

描述：自動比對申請書、IFU 與測試報告。 檢查點：產品名稱、型號、滅菌方式、保存期限、適應症。 輸出：不一致報告 (Discrepancy Report)，列出欄位名稱、文件 A 之數值、文件 B 之數值、差異程度。



FR-03: 語義問答 (Q&A Chatbot)

描述：使用者可針對特定案件進行對話式查詢。 功能：支援多輪對話，並具備「引用來源」功能，點擊引用可直接跳轉至 PDF 預覽器的對應頁面並高亮文字。



FR-04: 同類產品比對 (Peer Comparison)

描述：輸入對照器材 (Predicate Device) 許可證號，自動調閱歷史資料庫。 輸出：生成 實質等效性比對表 (SE Table)，並標註兩者差異處。 5.2 非功能性需求 (Non-Functional Requirements) NFR-01 回應時間：一般查詢 < 3 秒；複雜推理 < 30 秒。 NFR-02 併發量：支援至少 100 位審查員同時在線操作。 NFR-03 可用性：系統可用性達 99.9% (每月停機時間 < 43 分鐘)。 NFR-04 擴充性：向量資料庫需支援千萬級 Vectors 檢索，回應時間維持亞秒級 (Sub-second)。 6. 安全性與合規性 (Security & Compliance) 6.1 零信任架構 (Zero Trust Architecture) 身分鑑別：全面整合 TFDA 現有之 AD/LDAP，並強制啟用 MFA (多因子驗證)。 設備驗證：僅允許受列管之公務設備 (MDM) 存取後台 API。 最小權限原則 (Least Privilege)：依據職務 (Role) 設定資料存取權限，API Key 不落地，統一由 Key Vault 管理。 6.2 資料隱私保護 (Data Privacy) PII 去識別化演算法：



使用 NER (Named Entity Recognition) 識別姓名、電話、身分證號。



採用 Format-Preserving Encryption (FPE) 或遮罩 (Masking) 技術處理敏感字串。



例外管理：針對特定法律需求（如廠商名稱需保留以進行利益衝突迴避檢查），設定白名單。



資料加密：



傳輸中 (In-Transit)：TLS 1.3。



靜態 (At-Rest)：AES-256 加密儲存，金鑰由 HSM (Hardware Security Module) 管理。



6.3 稽核與監控 (Audit & Monitoring) 全路徑日誌：記錄每一次 AI 呼叫的 Prompt、Token Usage、Response 以及使用者 ID。 異常偵測：針對異常大量的資料下載或高頻率的 AI 查詢行為，設置自動告警 (Alerting)。 模型監控：定期評估模型輸出的品質，偵測是否出現 Model Drift 或有害內容。 7. 基礎設施與部署 (Infrastructure & Deployment) 7.1 Kubernetes 叢集規劃 建議採用 GKE (Google Kubernetes Engine) 或 AKS (Azure Kubernetes Service) 搭配地端節點 (Anthos/Arc)。



Node Pools:



System Pool: 運行 API Gateway, Ingress, Monitoring Agent。



App Pool: 運行 Web UI, Backend API (Auto-scaling enabled)。



AI Pool: 配備 GPU (NVIDIA T4/L4) 運行開源 OCR 模型與 Embedding 模型。



Vector DB Pool: 高記憶體配置 (High-Memory) 運行 Qdrant/Milvus。



7.2 基礎設施即程式碼 (IaC) - Terraform 範例



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

7.3 硬體資源估算 (地端部署選項) 若需完全地端部署 Taiwan-LLM (70B)，建議硬體規格：



GPU Server: 2x NVIDIA A100 (80GB) 或 4x NVIDIA A6000 Ada。 CPU: Dual AMD EPYC (64 cores)。 RAM: 512GB DDR5。 Storage: 10TB NVMe SSD (RAID 10) 供向量庫與原始檔存取。 8. 驗證與確效 (Validation & Verification) 本系統雖非醫療器材，但作為監管決策輔助工具，建議參照 GAMP 5 (Good Automated Manufacturing Practice) 與 CSV (Computer System Validation) 精神進行確效。



8.1 V-Model 驗證流程 使用者需求 (URS) ↔ 性能確認 (PQ): 確認系統在真實審查場景下能提升效率。 功能規格 (FS) ↔ 操作確認 (OQ): 測試各功能模組（如摘要、搜尋）是否符合預期輸出。 設計規格 (DS) ↔ 安裝確認 (IQ): 確認系統正確部署於指定環境且配置無誤。 8.2 AI 特定測試 (AI-Specific Testing) Ground Truth Evaluation: 建立包含 100 份歷史案件的「黃金標準測試集」，由資深審查員標註正確摘要與比對結果。 Hallucination Rate: 透過自動化腳本檢查 AI 生成內容中的數據是否真實存在於原始文件中（Fact-checking）。 Robustness Test: 測試掃描品質不佳、歪斜、含有浮水印的 PDF，確認 OCR 與 AI 的容錯能力。 Adversarial Attack: 測試惡意 Prompt 是否能繞過安全機制導出敏感資料。 8.3 持續監控 (Continuous Monitoring) 建立 Human-in-the-loop (HITL) 回饋機制：審查員可對 AI 輸出按讚/倒讚或修正。這些回饋數據將儲存於 Data Lake，用於下一版本的模型微調 (Fine-tuning)。



專案管理與實施時程 (Project Management) Phase 1: 基礎建設與 POC (Month 1-4) Month 1: 需求訪談、架構設計確認、採購雲端資源。 Month 2: 建立 RAG 基礎管道 (OCR + Vector DB)，完成資料清洗。 Month 3: 開發「智慧語義搜尋」原型，邀請 5 位核心使用者測試。 Month 4: POC 結案報告，評估技術可行性與修正方向。 Phase 2: 核心功能開發 (Month 5-10) Month 5-6: 開發「智慧摘要」與「一致性稽核」模組，整合 LLM API。 Month 7: 前端介面整合 (React UI)，開發側邊欄助手功能。 Month 8: 系統整合測試 (SIT)，串接身分驗證與現有資料庫。 Month 9: 使用者驗收測試 (UAT)，擴大至 20 位使用者。 Month 10: 資安弱點掃描與滲透測試，修正高風險漏洞。 Phase 3: 全面上線與優化 (Month 11-12+) Month 11: 正式環境部署 (Production Deployment)，教育訓練。 Month 12: 全面切換上線 (Go-live)，啟動維運監控。 On-going: 每季依據使用者回饋與新 AI 模型技術進行迭代更新。

效益分析 (Cost-Benefit Analysis) 10.1 質化效益 提升審查一致性：減少因人為疏忽或經驗落差導致的審查標準不一。 知識傳承：將資深審查員的隱性知識透過 Prompt Engineering 轉化為系統邏輯。 優化廠商體驗：減少因格式錯誤或簡單缺失導致的補件往返，加速產品上市。 10.2 量化效益 (ROI 估算) 假設 TFDA 每年受理 3,000 件醫材申請案，平均每案審查工時 40 小時。

初審效率提升：智慧分類與檢索節省 30% 時間 → 3,000 * 40 * 30% = 36,000 小時/年。 技術審查效率提升：智慧摘要與比對節省 20% 時間 → 3,000 * 40 * 20% = 24,000 小時/年。 總節省工時：約 60,000 小時/年。 若以專家審查員時薪 NT$ 1,000 計算，每年可創造 NT$ 6,000 萬 的時間價值。



扣除系統建置與維運成本 (預估首年 2,000 萬，次年起 500 萬)，ROI 預計在 第 6 個月 即可轉正。



風險管理 (Risk Management) 風險項目 發生機率 影響程度 緩解措施 (Mitigation Plan) AI 幻覺 (Hallucination) 中 高



介面強制顯示引用來源 (Citations)。

2. 提示詞加入 "若資訊不足請回答不知道" 約束。

3. 關鍵數據需人工二次確認。 資料外洩 (Data Leakage) 低 極高



採用 Azure OpenAI 企業版 (Zero Data Retention)。

2. 嚴格執行 PII 去識別化。

3. 定期資安稽核。 系統整合複雜度高 高 中



採用微服務架構，逐步替換舊模組。

2. 保持 API 介面相容性。

3. 建立詳細的 Swagger 文件。 使用者抗拒改變 中 中



早期邀請意見領袖 (KOL) 參與設計。

2. 設計直覺的 UI，降低學習門檻。

3. 強調 AI 為「助手」而非「取代者」。 運算成本超支 中 低



實作 Token 使用量監控與 Quota 限制。

2. 優化 Prompt 長度。

3. 善用 Cache 機制減少重複推理。



附錄：20 個綜合追蹤問題 (20 Follow-up Questions) 為確保本方案能順利落地並持續優化，建議專案團隊在後續規劃中深入探討以下問題：



AI 治理：TFDA 是否需要成立專門的 AI 倫理委員會來審核模型的決策邏輯？



法規調適：現行法規是否允許審查員引用 AI 生成的摘要作為正式審查意見的一部分？



模型更新：當 Azure OpenAI 發布新模型 (如 GPT-5) 時，系統的升級與驗證 SOP 為何？



多語言支援：系統對於非英文/中文（如日文、德文原廠報告）的處理能力與翻譯準確度如何驗證？



廠商端應用：未來是否考慮開放部分 AI 預審功能給廠商，讓其在送件前自行檢查缺失？



地端算力：若政策強制要求完全離線，TFDA 機房的電力與空調設施是否足以支撐高密度 GPU 伺服器？



人才培訓：如何培訓現有審查員具備基本的 Prompt Engineering 技巧，以更精準地指揮 AI？



錯誤責任：若因 AI 摘要錯誤導致審查誤判（如漏看嚴重副作用），責任歸屬如何界定？



資料標註：如何建立持續性的回饋機制，讓審查員的修正能有效回流至模型微調？



版權問題：RAG 資料庫中儲存的 ISO 標準、學術期刊是否涉及版權授權問題？



災難復原：若 Azure 區域性服務中斷，系統是否有降級 (Degrade) 運作模式或備援機制？



API 經濟：是否考慮將去識別化後的審查數據透過 API 開放給學研單位進行監管科學研究？



跨部會整合：本系統是否能與健保署或海關系統介接，實現醫材全生命週期管理？



使用者介面：針對習慣紙本作業的資深審查員，UI 設計是否有足夠的友善度與過渡輔助？



效能優化：針對數千頁的超大型 PDF，Chunking 與 Embedding 的處理策略是否會造成過長延遲？



測試資料：在開發階段如何取得足夠多樣且合規的真實案件數據進行模型測試？



預算編列：長期的雲端 Token 費用與 GPU 維護費用應編列於資本門還是經常門？



供應商鎖定：若未來需更換 AI 模型供應商 (如從 OpenAI 轉至 Google Gemini)，架構的抽換成本為何？



資安邊界：在地端與雲端進行資料交換時，如何確保 VPN 通道的絕對安全與監控？



國際接軌：本系統的資料標準是否符合 IMDRF (國際醫療器材監管機構論壇) 的規範，以利未來國際合作？

7.58k
Response
Preview
API
醫療器材查驗登記電子化送件系統導入智慧化審查之可行性方案
完整技術規格書

版本: V2.0-Detailed

日期: 2024年5月25日

作者: TFDA 智慧審查專案辦公室 / 系統架構師團隊

執行摘要 (Executive Summary)
1.1 策略願景
隨著醫療器材科技日新月異,<span style="color:coral">食品藥物管理署 (TFDA)</span> 面臨查驗登記案件量逐年攀升與技術複雜度增加的雙重挑戰。本計畫旨在透過 <span style="color:coral">人工智慧 (AI)</span> 與 <span style="color:coral">自然語言處理 (NLP)</span> 技術,將現行「<span style="color:coral">電子化送件系統</span>」升級為「<span style="color:coral">智慧審查輔助平台</span>」。

根據2024年第一季的使用者調查(N=41),雖然電子化送件在資料存取便利性上獲得肯定(4.05/5),但 <span style="color:coral">審查效率</span> 仍受限於海量<span style="color:coral">非結構化文件</span>的檢索與比對。本方案將導入 <span style="color:coral">RAG (Retrieval-Augmented Generation) 架構</span>,預期可縮短30%初審時間,並提升技術文件審查的一致性。

1.2 專案目標
短期目標 (6個月):

完成特定類別(如<span style="color:coral">軟體醫材 SaMD</span>)的<span style="color:coral">智慧摘要</span>與<span style="color:coral">語義搜尋</span> POC
驗證AI對醫療法規文本的理解能力
中期目標 (12個月):

建立 <span style="color:coral">跨文件自動稽核</span> 機制
自動比對申請書、IFU與測試報告的規格一致性
錯誤檢出率達90%以上
長期目標 (24個月):

實現 <span style="color:coral">全生命週期智慧管理</span>
從送件前預審到<span style="color:coral">上市後監督 (PMS)</span> 的數據貫通
1.3 關鍵成效指標 (KPIs)
指標項目	現況基準	目標值
<span style="color:coral">檢索時間</span>	平均15分鐘/案	30秒/案
<span style="color:coral">摘要準確度</span>	N/A	ROUGE-L分數達0.8以上
<span style="color:coral">系統可用性</span>	尖峰時段經常卡頓	API回應時間 < 3秒 (P95)
<span style="color:coral">審查工時節省</span>	40小時/案	節省30% (28小時/案)
2. 現況分析與痛點深掘 (Context & Pain Points)
2.1 使用者旅程痛點分析
透過深度訪談與問卷分析,我們將審查流程中的痛點對應至具體技術缺口:

審查階段	使用者痛點 (Pain Points)	技術缺口 (Technical Gap)	影響評估
<span style="color:coral">案件分派與初審</span>	需逐一打開PDF確認產品分類,耗時且易遺漏關鍵屬性	缺乏 <span style="color:coral">自動分類 (Auto-Classification)</span> 與關鍵字提取能力	行政效率低落,案件積壓
<span style="color:coral">實質審查 (技術文件)</span>	臨床前測試報告動輒數百頁,難以快速定位「失敗(Failed)」或「偏差(Deviated)」的測試結果	缺乏 <span style="color:coral">語義理解 (Semantic Understanding)</span> 與異常值偵測機制	審查品質依賴個人經驗,存在漏看風險
<span style="color:coral">一致性確認</span>	產品說明書(IFU)與測試報告中的規格數據(如滅菌參數、電氣規格)常有出入,需人工來回比對	缺乏 <span style="color:coral">跨文件實體鏈接 (Cross-Document Entity Linking)</span> 能力	增加補件往返次數,延宕上市時間
<span style="color:coral">歷史案件參照</span>	難以快速調閱過去類似案件的決策依據與核准規格,導致審查標準不一	缺乏 <span style="color:coral">向量化知識庫 (Vectorized Knowledge Base)</span>	審查一致性不足,廠商抱怨標準浮動
2.2 系統效能瓶頸
現有系統架構在面對大檔案(100MB+)下載與預覽時,常出現 <span style="color:coral">Timeout</span>。調查顯示85%使用者曾在尖峰時段遭遇卡頓。這主要源於:

同步處理機制: 檔案上傳與病毒掃描佔用Web Server執行緒
資料庫索引不足: 傳統關聯式資料庫難以支撐全文檢索需求
缺乏快取機制: 重複查詢未能有效利用快取降低負載
2.3 現行審查流程時序圖

Copy
廠商送件 → 行政初審(3-5天) → 技術分派(1-2天) → 實質審查(20-30天) 
→ 內部會議(2-3天) → 補件通知(若需要) → 再次審查 → 核准/不核准
平均總時程: 60-90個工作天

補件率: 約45%

主要補件原因: 文件不一致(32%)、規格描述不清(28%)、測試報告缺漏(25%)

3. 系統架構設計 (System Architecture)
本計畫將採用 <span style="color:coral">混合雲微服務架構 (Hybrid Cloud Microservices)</span>,以平衡資安合規與AI運算需求。

3.1 邏輯架構視圖
展示層 (Presentation Layer)
Smart Review Portal
基於 <span style="color:coral">React.js</span> 開發的SPA
整合PDF Viewer與AI側邊欄
支援響應式設計(RWD)適配各種螢幕尺寸
Admin Console
模型監控與系統設定介面
使用者權限管理
稽核日誌查詢
API閘道層 (API Gateway)
負責<span style="color:coral">請求路由</span>、<span style="color:coral">Rate Limiting</span>、<span style="color:coral">OAuth2驗證</span>與API稽核
使用 <span style="color:coral">Kong</span> 或 <span style="color:coral">Azure API Management</span>
實作API版本管理與向下相容性
業務服務層 (Business Services)
Submission Service
處理送件資料CRUD
管理案件生命週期狀態
Workflow Engine
基於 <span style="color:coral">Camunda</span> 或 <span style="color:coral">Temporal</span> 處理審查流程狀態機
支援複雜的條件分支與並行審查
Notification Service
處理Email與站內通知
支援多通道推播(Email/SMS/App)
智慧服務層 (Intelligence Services)
Ingestion Pipeline
負責檔案的<span style="color:coral">OCR</span>、<span style="color:coral">切塊(Chunking)</span>、<span style="color:coral">Embedding</span>
支援批次與即時處理模式
Inference Service
封裝LLM呼叫
實作<span style="color:coral">Prompt Template管理</span>與<span style="color:coral">Context Window優化</span>
提供統一的推理介面抽象層
Verification Service
執行一致性比對邏輯
實作規則引擎與AI混合驗證
資料持久層 (Data Layer)
Object Storage
使用 <span style="color:coral">S3/MinIO</span>
儲存原始PDF與解析後的JSON
實作分層儲存策略(熱/溫/冷資料)
Vector Database
使用 <span style="color:coral">Qdrant/Milvus</span>
儲存文件Embedding向量
支援混合檢索(Dense + Sparse)
Relational DB
使用 <span style="color:coral">PostgreSQL</span>
儲存結構化案件資料與使用者權限
實作讀寫分離與連線池管理
3.2 關鍵技術決策
OCR引擎選型
方案	優勢	劣勢	建議場景
<span style="color:coral">Azure AI Document Intelligence</span>	準確率高、支援表格識別、免維護	成本較高、需網路連線	一般品質文件
<span style="color:coral">PaddleOCR</span>	開源免費、可離線運行	需自行微調醫療字彙、維護成本高	高度機密文件
<span style="color:coral">Tesseract + 預處理</span>	成熟穩定、社群支援好	表格識別能力弱	純文字文件
決策: 採用雙軌制,一般文件使用Azure AI,機密文件使用PaddleOCR

向量模型選型
首選: <span style="color:coral">text-embedding-3-large</span>
支援8192 tokens輸入長度
優異的多語言表現
備選: <span style="color:coral">BGE-M3</span>
開源可本地部署
支援100+語言
可針對醫療領域進行微調
非同步佇列架構
使用 <span style="color:coral">RabbitMQ</span> 或 <span style="color:coral">Apache Kafka</span> 解耦檔案上傳與AI分析流程:


Copy
上傳請求 → Message Queue → Worker Pool → AI處理 → 結果快取 → 非同步通知
優勢:

解決尖峰效能問題
支援失敗重試機制
提供處理進度追蹤
3.3 混合雲資料流設計
資料流向示意

Copy
[TFDA本地機房]
   ↓ (原始PDF上傳)
[去識別化模組] ← 使用BERT-NER遮罩PII
   ↓ (去識別化文本)
[加密通道 - VPN/TLS]
   ↓
[Azure OpenAI] → 僅接收文本特徵
   ↓ (推理結果)
[本地端重組模組] → Re-identification
   ↓
[審查員介面呈現]
安全控制點
資料落地: 所有原始送件資料儲存於TFDA本地機房或<span style="color:coral">政府私有雲(Gov Cloud)</span>
去識別化處理:
在本地端透過<span style="color:coral">NLP模型(如BERT-NER)</span>遮罩PII
遮罩內容包含
、身分證號、聯絡方式、地址
雲端運算:
僅將去識別化後的文本特徵送往Azure OpenAI進行推理
使用Azure OpenAI <span style="color:coral">企業版Zero Data Retention政策</span>
結果回傳:
推理結果回傳至本地端
與原始資料重新映射(<span style="color:coral">Re-identification</span>)後呈現給審查員
網路架構

Copy
[審查員工作站]
    ↓ (HTTPS/TLS 1.3)
[TFDA DMZ - API Gateway]
    ↓ (內部網路)
[應用伺服器叢集]
    ↓ (專線/ExpressRoute)
[Azure Taiwan Region]
4. AI核心技術方案 (AI Core Technology)
4.1 RAG (Retrieval-Augmented Generation) 管道設計
為解決LLM的<span style="color:coral">幻覺(Hallucination)</span>問題,本系統嚴格遵循RAG架構。

資料前處理 (Indexing Phase)
1. Parsing (解析)

解析PDF結構,保留標題層級(H1-H3)與表格關聯
識別文件元素:
頁首/頁尾
圖表與圖說
表格結構
引用文獻
2. Chunking (切塊)

採用 <span style="color:coral">Semantic Chunking策略</span>,而非固定字數切割
確保語意完整性
每個Chunk約500-1000 tokens
保留上下文重疊(Overlap)100 tokens
切塊策略範例:

python

Copy
# 語義切塊邏輯
def semantic_chunk(document, max_tokens=1000, overlap=100):
    chunks = []
    current_chunk = []
    
    for section in document.sections:
        if is_natural_boundary(section):  # 章節邊界
            if len(current_chunk) > 0:
                chunks.append(current_chunk)
                current_chunk = get_overlap(current_chunk, overlap)
        current_chunk.append(section)
    
    return chunks
3. Metadata Tagging (元資料標註)
自動標註以下資訊:

<span style="color:coral">文件類型</span>
、證書、說明書、臨床資料
<span style="color:coral">日期</span>
、測試日期
<span style="color:coral">版本號</span>
<span style="color:coral">測試標準</span>
/IEC編號
<span style="color:coral">產品資訊</span>
、型號、規格
檢索階段 (Retrieval Phase)
1. Hybrid Search (混合搜尋)

結合 <span style="color:coral">關鍵字搜尋(BM25)</span> 與 <span style="color:coral">向量搜尋(Dense Vector)</span>:


Copy
使用者查詢 → 
    ├─ BM25搜尋 → 候選文件集A (基於詞頻)
    └─ 向量搜尋 → 候選文件集B (基於語義)
         ↓
    RRF融合排序 → Top-K結果
RRF (Reciprocal Rank Fusion) 演算法:


Copy
RRF_score = Σ(1 / (k + rank_i))
其中 k=60 (常數), rank_i 為該文件在第i個排序中的名次
2. Re-ranking (重新排序)

使用 <span style="color:coral">Cross-Encoder模型(如BGE-Reranker)</span> 對初步檢索結果進行精細排序:

python

Copy
# 重新排序流程
initial_results = hybrid_search(query, top_k=50)
reranked_results = cross_encoder.predict([
    (query, doc.content) for doc in initial_results
])
final_results = sort_by_score(reranked_results)[:10]
3. Pre-filtering (前置過濾)

利用Metadata進行快速過濾:

日期範圍篩選
文件類型篩選
產品分類篩選
生成階段 (Generation Phase)
1. Context Assembly (上下文組裝)

將高相關性Chunks組裝至Prompt:


Copy
系統角色設定 (200 tokens)
    ↓
檢索到的相關文件 (3000 tokens)
    ↓
使用者問題 (100 tokens)
    ↓
輸出格式指示 (100 tokens)
總計: ~3400 tokens (留有buffer)
2. Citation Injection (引用注入)

要求模型在生成的每一句話後標註來源Chunk ID:

markdown

Copy
產品的滅菌方式為環氧乙烷滅菌[^1],滅菌週期參數為溫度50±5°C[^2]...

[^1]: 來源:IFU-2024-001.pdf, 第5頁
[^2]: 來源:Sterilization-Report-2024.pdf, 第12頁
前端介面實作「點擊溯源」功能,點擊引用編號可跳轉至PDF預覽器的對應頁面並高亮文字。

3. Output Validation (輸出驗證)

python

Copy
def validate_output(generated_text, source_chunks):
    # 檢查是否包含引用
    if not has_citations(generated_text):
        return False, "缺少引用來源"
    
    # 檢查引用的Chunk ID是否存在
    citations = extract_citations(generated_text)
    for cit in citations:
        if cit.chunk_id not in [c.id for c in source_chunks]:
            return False, f"無效的引用ID: {cit.chunk_id}"
    
    # 事實一致性檢查
    facts = extract_facts(generated_text)
    for fact in facts:
        if not verify_fact_in_chunks(fact, source_chunks):
            return False, f"無法驗證的事實: {fact}"
    
    return True, "驗證通過"
4.2 模型選型矩陣
評估維度	<span style="color:coral">Azure OpenAI (GPT-4o)</span>	<span style="color:coral">Local LLM (Llama 3 70B)</span>	<span style="color:coral">Taiwan-LLM</span>
邏輯推理能力	極高(適合複雜比對)	高(需微調)	中(適合在地文化內容)
醫療專業知識	高(預訓練資料豐富)	中	中
資料隱私	中(企業級合規)	極高(完全離線)	極高(完全離線)
維護成本	低(API呼叫)	高(需GPU叢集維運)	高
回應速度	取決於網路與TPM	取決於硬體算力	取決於硬體算力
多語言支援	優秀(100+語言)	良好(需微調)	以中文為主
成本估算	$0.01/1K tokens	硬體投資+電費	硬體投資+電費
決策建議
採用<span style="color:coral">雙軌制</span>:

一般行政文件與公開技術資料
使用Azure OpenAI以獲取最佳效果
適用場景
、公開測試標準、一般技術文件
高度機密或涉及個資之文件
使用Local LLM處理
適用場景
、專利技術、廠商機密資訊
4.3 提示詞工程 (Prompt Engineering) 策略
1. Role Prompting (角色設定)

Copy
你是台灣食品藥物管理署(TFDA)的資深醫療器材審查員,
具有15年以上的審查經驗,專精於體外診斷醫療器材(IVD)
與主動式植入物的技術評估...
2. Chain-of-Thought (思維鏈)
引導模型逐步推理:


Copy
請按照以下步驟分析:

步驟1: 識別產品宣稱的臨床效能指標
步驟2: 在測試報告中找出對應的實測數據
步驟3: 比對宣稱值與實測值是否一致
步驟4: 評估測試方法是否符合相關標準
步驟5: 給出最終結論(一致/不一致)並說明理由
3. Few-Shot Learning (少樣本學習)
在Prompt中提供3-5個高品質的歷史審查範例:

markdown

Copy
【範例1:一致性比對 - 正確案例】
申請書宣稱:「產品滅菌保證水平(SAL)達10^-6」
測試報告數據:「完成3個批次驗證,SAL實測值為2.1×10^-7」
結論:✓ 一致。實測值優於宣稱值。

【範例2:一致性比對 - 錯誤案例】  
申請書宣稱:「產品適用於-20°C至50°C環境」
測試報告數據:「溫度測試範圍0°C至40°C」
結論:✗ 不一致。測試範圍未涵蓋宣稱的溫度下限。

現在請分析以下案件:
[實際案件內容]
4. Constraint Injection (約束注入)
明確限制模型行為:


Copy
【重要約束】
1. 若資訊不足以做出判斷,請明確回答「資訊不足」,不要推測
2. 所有數據必須引用具體來源(文件名+頁碼)
3. 不要使用「可能」「也許」等模糊用語
4. 如發現矛盾,必須明確指出並列舉證據
5. Output Format Specification (輸出格式規範)
json

Copy
{
  "summary": "檢查摘要(限200字)",
  "consistency_check": {
    "claimed_specs": ["宣稱規格1", "宣稱規格2"],
    "tested_specs": ["測試規格1", "測試規格2"],
    "matches": [
      {
        "item": "項目名稱",
        "status": "一致/不一致/資訊不足",
        "evidence": "證據來源",
        "comments": "說明"
      }
    ]
  },
  "risk_flags": ["潛在風險1", "潛在風險2"],
  "recommendations": "審查建議"
}
5. 軟體需求規格 (Software Requirements Specification)
5.1 功能性需求 (Functional Requirements)
FR-01: 智慧文件摘要 (Smart Summarization)
描述:

系統應能針對「<span style="color:coral">臨床前測試報告</span>」生成結構化摘要。

輸入:

PDF/Word格式報告
檔案大小上限
頁數範圍
處理流程:


Copy
PDF上傳 → OCR識別 → 章節分割 → 關鍵資訊提取 → 結構化輸出
輸出格式:

欄位	內容說明	範例
測試目的	測試項目與目標	"驗證產品生物相容性符合ISO 10993-5"
測試標準	引用的國際/國家標準	"ISO 10993-5
, ISO 10993-10
"
測試方法	測試程序簡述	"細胞毒性測試(MTT法), 皮膚刺激性測試"
關鍵結果數據	量化數據摘要	"細胞存活率>70%(通過), 刺激指數=0.8(輕微刺激)"
結論	Pass/Fail判定	"✓ Pass - 符合生物相容性要求"
驗收標準:

對於長度>50頁的報告,摘要生成時間<2分鐘
準確擷取測試標準編號(如ISO 10993-5)
<span style="color:coral">ROUGE-L分數</span> ≥ 0.75(與人工摘要比對)
錯誤處理:

若OCR識別率<80%,提示「掃描品質不佳,建議重新上傳」
若無法識別測試標準,標註「⚠ 未識別到標準測試方法」
FR-02: 跨文件一致性稽核 (Cross-Document Consistency Check)
描述:

自動比對<span style="color:coral">申請書</span>、<span style="color:coral">IFU(產品說明書)</span>與<span style="color:coral">測試報告</span>的一致性。

檢查點矩陣:

檢查項目	申請書欄位	IFU章節	測試報告對應
產品名稱	第1.1節	封面	報告首頁
型號規格	第1.2節	技術規格表	樣品描述
滅菌方式	第3.4節	儲存與處理	滅菌驗證報告
保存期限	第3.5節	標籤資訊	穩定性測試結論
適應症	第2.1節	預期用途	臨床評估報告
禁忌症	第2.2節	警語與注意事項	風險分析
輸出範例:

markdown

Copy
## 不一致報告 (Discrepancy Report)

### ⚠ 發現3處不一致

#### 1. 滅菌方式不一致 (嚴重)
- **申請書** (第3.4節): 「環氧乙烷(EO)滅菌」
- **IFU** (第5頁): 「環氧乙烷滅菌,滅菌週期:12小時」  
- **測試報告** (第8頁): 「輻射滅菌(Gamma射線, 25kGy)」
- **差異評估**: 🔴 嚴重不一致 - 滅菌方法完全不同
- **建議**: 釐清實際滅菌方式並統一修正

#### 2. 保存期限不一致 (中度)
- **申請書**: 「3年」
- **IFU**: 「36個月」
- **測試報告**: 「穩定性測試完成24個月驗證」
- **差異評估**: 🟡 中度不一致 - 測試數據未支撐宣稱期限
- **建議**: 補充額外12個月穩定性數據或調整宣稱期限

#### 3. 型號表示不一致 (輕微)
- **申請書**: 「Model: ABC-100」
- **IFU**: 「型號:ABC100」  
- **差異評估**: 🟢 輕微 - 僅格式差異(有無連字號)
- **建議**: 統一型號表示格式
演算法:

python

Copy
def cross_check_consistency(申請書, IFU, 測試報告):
    結果 = []
    
    for 檢查項目 in 檢查點清單:
        值A = 從申請書提取(檢查項目)
        值B = 從IFU提取(檢查項目)
        值C = 從測試報告提取(檢查項目)
        
        if not 值相似(值A, 值B, 值C):
            差異程度 = 評估差異嚴重性(值A, 值B, 值C)
            結果.append({
                "項目": 檢查項目,
                "嚴重度": 差異程度,
                "詳情": [值A, 值B, 值C],
                "建議": 生成建議(差異程度)
            })
    
    return 結果
驗收標準:

錯誤檢出率(Recall) ≥ 90%
誤報率(False Positive Rate) ≤ 10%
處理時間
(3份) < 5分鐘
FR-03: 語義問答 (Conversational Q&A Chatbot)
描述:

使用者可針對特定案件進行<span style="color:coral">對話式查詢</span>。

使用情境範例:


Copy
審查員: 請問這個產品的臨床試驗樣本數是多少?

AI助手: 根據臨床評估報告(Clinical-Eval-2024.pdf, 第23頁),
       本產品進行了一項前瞻性臨床試驗,納入120名受試者,
       其中試驗組60名,對照組60名[^1]。

       [^1] Clinical-Eval-2024.pdf, Section 4.2 "Study Design"

審查員: 有無受試者退出?

AI助手: 是的,報告第28頁提到共有8名受試者中途退出[^2]:
       - 試驗組: 5名(失聯3名,違反納入標準2名)
       - 對照組: 3名(個人因素2名,搬遷1名)
       完成試驗人數為112名,完成率93.3%。

       [^2] Clinical-Eval-2024.pdf, Table 6 "Subject Accountability"

審查員: 這個完成率是否符合統計檢定力要求?

AI助手: 根據報告第15頁的樣本數計算[^3],原始設計基於80%檢定力
       (Power)、α=0.05、預期效果量(Effect Size)=0.5,
       計算所需樣本數為每組53名。實際完成數(試驗組55名、對照組57名)
       高於此要求,因此✓符合統計檢定力要求。

       [^3] Clinical-Eval-2024.pdf, Section 3.4 "Sample Size Calculation"
技術特性:

多輪對話管理
維護對話上下文(Context)
支援代詞消解(如「這個產品」「該試驗」)
引用溯源功能
每個回答必須標註來源
點擊引用編號→跳轉PDF預覽器→高亮對應文字
智慧提示
當使用者問題過於模糊時,提供澄清問題建議
範例:「您是想詢問『臨床試驗樣本數』還是『上市後監測樣本數』?」
介面設計:


Copy
┌─────────────────────────────────────────┐
│  📄 案件編號: MD-2024-12345              │
├─────────────────────────────────────────┤
│  💬 AI審查助手                          │
│                                         │
│  👤 請說明產品的主要風險                │
│                                         │
│  🤖 根據風險分析報告,識別出以下主要風險: │
│     1. 電氣危害(風險等級:中)[^1]        │
│     2. 生物相容性風險(風險等級:低)[^2]  │
│     3. 軟體故障風險(風險等級:高)[^3]    │
│                                         │
│     [點擊查看完整風險矩陣]               │
│                                         │
│  👤 ________________________________    │
│     [送出]                              │
└─────────────────────────────────────────┘
驗收標準:

回答準確率 ≥ 85%(與專家判斷比對)
回應時間 < 10秒
支援至少10輪連續對話不失去上下文
FR-04: 同類產品比對 (Peer Product Comparison)
描述:

輸入<span style="color:coral">對照器材(Predicate Device)</span>許可證號,自動調閱歷史資料庫並生成比對表。

輸入:

對照器材許可證號(如
)
申請案件編號
處理流程:


Copy
輸入對照器材許可證號 
    ↓
查詢歷史資料庫
    ↓
提取關鍵技術規格
    ↓
與新申請案比對
    ↓
生成實質等效性比對表(SE Table)
    ↓
標註差異處並評估影響
輸出範例:

markdown

Copy
# 實質等效性比對表 (Substantial Equivalence Table)

## 基本資訊
| 項目 | 對照器材 | 申請產品 |
|------|---------|---------|
| 許可證號 | 衛部醫器製字第012345號 | (新申請) |
| 產品名稱 | ABC血糖監測系統 | XYZ血糖監測系統 |
| 製造廠 | ABC醫療科技股份有限公司 | XYZ生技股份有限公司 |
| 核准日期 | 2020-05-15 | - |

## 技術規格比對

### ✅ 相同之處
| 項目 | 規格 |
|------|------|
| 預期用途 | 體外檢測毛細血管全血中的葡萄糖濃度 |
| 檢測原理 | 葡萄糖氧化酶(GOD)生化反應 |
| 樣本類型 | 毛細血管全血 |
| 量測範圍 | 20-600 mg/dL |

### ⚠️ 差異之處
| 項目 | 對照器材 | 申請產品 | 影響評估 |
|------|---------|---------|---------|
| **準確度** | ±15% | ±10% | 🟢 改善 - 準確度提升 |
| **檢測時間** | 8秒 | 5秒 | 🟢 改善 - 速度更快 |
| **樣本量** | 1.0 μL | 0.5 μL | 🟢 改善 - 需血量減少 |
| **數據傳輸** | 無 | 藍牙5.0 | 🟡 新增功能 - 需評估軟體風險 |
| **電源** | 鈕扣電池(CR2032) | 可充電鋰電池 | 🟡 變更 - 需評估電氣安全 |

## 🔍 風險評估建議

### 新增功能風險
**藍牙數據傳輸:**
- ⚠️ 需評估資安風險(數據加密、配對安全)
- ⚠️ 需評估電磁相容性(EMC)是否受影響
- 📋 建議補充:
  - IEC 62304軟體生命週期文件
  - 網路安全風險分析
  - EMC測試報告

### 變更設計風險  
**可充電電池:**
- ⚠️ 需評估充電安全(過充保護、溫度監控)
- ⚠️ 需評估電池壽命對產品可用性的影響
- 📋 建議補充:
  - 電池安全測試報告(IEC 62133)
  - 使用壽命驗證數據

## 結論

✅ **基本等效性:** 預期用途、檢測原理、量測範圍相同  
🟢 **效能改善:** 準確度、速度、樣本量均有提升  
🟡 **新增風險:** 軟體(藍牙)、電氣(可充電電池)需額外評估

**建議:** 可採用實質等效途徑(510(k) Equivalent),但需補充:
1. 軟體確效文件
2. 網路安全評估
3. 電池安全測試
4. EMC測試(涵蓋藍牙模組)
驗收標準:

能正確調閱90%以上的歷史案件
自動識別>15項關鍵技術規格
差異標註準確率 ≥ 85%
5.2 非功能性需求 (Non-Functional Requirements)
NFR-01: 效能需求 (Performance Requirements)
指標	要求	測試方法
一般查詢回應時間	< 3秒(P95)	Apache JMeter負載測試
複雜推理回應時間	< 30秒(P95)	實際案件模擬測試
文件上傳速度	100MB檔案<60秒	網路頻寬
併發使用者數	≥ 100人同時在線	負載測試達100 concurrent users
資料庫查詢	< 500ms(單次查詢)	SQL Query分析
向量檢索速度	< 1秒(千萬級向量)	Qdrant Benchmark
效能監控儀表板:

即時API回應時間分布圖
Token使用量趨勢
系統資源使用率(CPU/Memory/GPU)
慢查詢警示
NFR-02: 可用性需求 (Availability Requirements)
目標: <span style="color:coral">99.9%可用性</span> (每月停機時間 < 43分鐘)

實現策略:

高可用性架構

Copy
負載均衡器(Load Balancer)
    ↓
[API Server 1] [API Server 2] [API Server 3]
    ↓
主資料庫(Master) ← 複寫 → 備援資料庫(Replica)
容錯機制
API Gateway自動重試(Retry)
熔斷器(Circuit Breaker)模式
優雅降級(Graceful Degradation)
備份策略
資料庫
+每小時增量備份
物件儲存
(Cross-Region Replication)
保留週期
災難復原(Disaster Recovery)
RTO (Recovery Time Objective): < 4小時
RPO (Recovery Point Objective): < 1小時
NFR-03: 擴充性需求 (Scalability Requirements)
垂直擴展(Scale-up):

資料庫
向量資料庫
水平擴展(Scale-out):

API服務
向量資料庫
(Sharding)至10個節點
容量規劃:

資源	現行容量	3年後預估	擴充方案
案件數	3,000件/年	5,000件/年	資料庫分區(Partition by year)
文件總量	50TB	150TB	物件儲存自動擴容
向量數	500萬	2,000萬	向量庫水平擴展
併發使用者	100人	200人	Auto-scaling group
NFR-04: 安全性需求 (Security Requirements)
詳見第6章「安全性與合規性」

NFR-05: 維護性需求 (Maintainability Requirements)
程式碼品質標準:

程式碼覆蓋率(Code Coverage) ≥ 80%
複雜度(Cyclomatic Complexity) ≤ 15
技術債務比率(Technical Debt Ratio) < 5%
文件化要求:

API文件
/OpenAPI 3.0
系統架構圖
Model
操作手冊
(Troubleshooting)指南
日誌規範:

json

Copy
{
  "timestamp": "2024-05-25T14:30:00Z",
  "level": "INFO",
  "service": "inference-service",
  "user_id": "reviewer_001",
  "case_id": "MD-2024-12345",
  "action": "generate_summary",
  "duration_ms": 2340,
  "tokens_used": 3240,
  "model": "gpt-4o",
  "result": "success"
}
NFR-06: 可用性(Usability)需求
目標: <span style="color:coral">系統可用性評分(SUS)</span> ≥ 75分

設計原則:

一致性: 遵循Material Design或Fluent Design規範
回饋明確: 任何操作在500ms內提供視覺回饋
錯誤容忍: 提供Undo功能,避免誤操作
無障礙: 符合WCAG 2.1 AA級標準
使用者測試:

每季進行可用性測試(N≥10)
任務完成率 ≥ 90%
任務完成時間較舊系統縮短 ≥ 30%
6. 安全性與合規性 (Security & Compliance)
6.1 零信任架構 (Zero Trust Architecture)
本系統採用<span style="color:coral">零信任安全模型</span>,遵循「永不信任,持續驗證」原則。

核心原則
明確驗證 (Verify Explicitly)
每次請求都須驗證身分、設備、位置
不因內網環境而降低安全標準
最小權限存取 (Least Privilege Access)
Just-In-Time (JIT) 權限授予
基於角色的存取控制(RBAC)
假設入侵 (Assume Breach)
微分段(Micro-segmentation)
加密所有資料傳輸
實作架構

Copy
使用者登入
    ↓
[身分識別提供者 (IdP)]
    ↓ (SAML/OIDC)
[多因子驗證 (MFA)] ← 推播通知/OTP
    ↓
[設備健康檢查] ← MDM驗證
    ↓
[條件式存取政策引擎]
    ├─ 檢查:IP位置、設備合規性、風險評分
    └─ 決策:允許/拒絕/要求額外驗證
    ↓
[API Gateway - Token驗證]
    ↓
[微服務] ← 服務間mTLS加密
身分鑑別機制
1. 單一登入 (SSO) 整合

整合TFDA現有 <span style="color:coral">Active Directory (AD) / LDAP</span>
支援SAML 2.0與OpenID Connect協定
2. 多因子驗證 (MFA)
強制啟用,支援:

行動裝置推播通知(Push Notification)
時間型一次性密碼(TOTP)
硬體安全金鑰(FIDO2)
實施策略:


Copy
一般查詢操作 → 單因子(帳號密碼)
敏感資料存取 → 雙因子(密碼+OTP)
關鍵決策操作 → 三因子(密碼+OTP+生物識別)
3. 設備驗證

僅允許受列管之公務設備(MDM管理)存取後台API
設備需符合:
作業系統版本需在支援期內
已安裝防毒軟體且病毒碼為最新
已啟用磁碟加密
未越獄(Jailbreak)或Root
4. 最小權限原則實作

角色	權限	資料存取範圍
系統管理員	系統設定、使用者管理	無法存取案件實質內容
一般審查員	檢視分派案件、使用AI輔助	僅自己負責案件
資深審查員	上述+歷史案件查詢	所有已結案案件
主管	上述+審批決策、統計報表	全部案件(唯讀)
稽核員	檢視操作日誌	僅Metadata,無案件內容
API Key管理:

不落地
<span style="color:coral">Azure Key Vault</span> 或 <span style="color:coral">HashiCorp Vault</span> 管理
自動輪替
權限綁定
Key綁定特定服務+IP白名單
6.2 資料隱私保護 (Data Privacy)
6.2.1 個人識別資訊 (PII) 去識別化
識別範圍:

姓名(中英文)
國民身分證號、護照號碼
聯絡電話、電子郵件
住址
病歷號碼
其他可直接或間接識別個人之資訊
技術方案:

1. 命名實體識別 (Named Entity Recognition, NER)

使用微調過的<span style="color:coral">BERT-NER模型</span>:

python

Copy
from transformers import pipeline

# 載入醫療領域微調模型
ner_pipeline = pipeline(
    "ner",
    model="tfda/bert-ner-medical-zh",
    aggregation_strategy="simple"
)

text = "王小明(A123456789)於2024年5月就診..."
entities = ner_pipeline(text)

# 輸出: [
#   {"entity": "PERSON", "word": "王小明", "start": 0, "end": 3},
#   {"entity": "ID_NUMBER", "word": "A123456789", "start": 4, "end": 14}
# ]
2. 遮罩策略

資訊類型	遮罩方式	範例
姓名	保留姓氏+星號	王**
身分證號	保留前2後2碼	A1******89
電話	保留區碼	02--
地址	保留至區級	台北市大安區***
Email	保留網域	****@tfda.gov.tw
3. Format-Preserving Encryption (FPE)

對於需要還原的敏感欄位(如內部追蹤用的病歷號),使用<span style="color:coral">格式保留加密</span>:

python

Copy
from ff3 import FF3Cipher

# 使用AES-256作為基礎
key = os.environ['FPE_KEY']  # 從Key Vault取得
cipher = FF3Cipher(key, tweak="醫材審查")

病歷號 = "MR20240525001"
加密病歷號 = cipher.encrypt(病歷號)  # "YH85139402706"
# 保持相同長度與字元集,但無法逆推原值(除非有金鑰)
4. 例外管理 - 白名單機制

針對特定法律需求保留的資訊:

廠商名稱: 需保留以進行<span style="color:coral">利益衝突迴避檢查</span>
產品名稱/型號: 需完整保留
測試機構名稱: 需保留以驗證資格
設定白名單避免誤遮罩:

json

Copy
{
  "whitelist_entities": [
    {"type": "COMPANY", "names": ["台灣醫療器材公司", "ABC Medical Inc."]},
    {"type": "TEST_LAB", "pattern": ".*實驗室|.*Lab"}
  ]
}
6.2.2 資料加密
1. 傳輸中加密 (Encryption in Transit)

使用 <span style="color:coral">TLS 1.3</span>
強制HTTPS,HTTP自動重導向
憑證管理:
使用EV SSL憑證
自動續約(Let's Encrypt + cert-manager)
憑證透明度(CT)監控
TLS設定範例 (Nginx):

nginx

Copy
ssl_protocols TLSv1.3;
ssl_ciphers 'TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256';
ssl_prefer_server_ciphers on;
ssl_session_timeout 1d;
ssl_session_cache shared:SSL:50m;
ssl_stapling on;
ssl_stapling_verify on;
2. 靜態加密 (Encryption at Rest)

使用 <span style="color:coral">AES-256-GCM</span>
金鑰管理
<span style="color:coral">HSM (Hardware Security Module)</span>管理
分層加密架構:


Copy
[資料加密金鑰 (DEK)] ← 加密資料
        ↓ (加密)
[金鑰加密金鑰 (KEK)] ← 儲存於HSM
        ↓ (保護)
[主金鑰 (Master Key)] ← 物理安全控制
資料庫加密:

sql

Copy
-- PostgreSQL透明資料加密(TDE)
CREATE EXTENSION pgcrypto;

CREATE TABLE 案件資料 (
    案件編號 VARCHAR(20) PRIMARY KEY,
    申請人資訊 BYTEA,  -- 加密儲存
    ...
);

-- 寫入加密
INSERT INTO 案件資料 VALUES (
    'MD-2024-001',
    pgp_sym_encrypt('敏感資料', '從Key Vault取得的DEK')
);

-- 讀取解密
SELECT 
    案件編號,
    pgp_sym_decrypt(申請人資訊::bytea, '從Key Vault取得的DEK') 
FROM 案件資料;
3. 金鑰生命週期管理


Copy
金鑰產生 → 金鑰啟用 → 定期輪替(每年) → 金鑰歸檔 → 金鑰銷毀(7年後)
金鑰輪替策略:
DEK
KEK
舊金鑰保留期
(配合醫療法規保存年限)
6.3 稽核與監控 (Audit & Monitoring)
6.3.1 全路徑日誌 (Comprehensive Logging)
日誌等級定義:

等級	用途	範例
DEBUG	開發除錯	"執行SQL: SELECT * FROM..."
INFO	一般操作記錄	"使用者登入成功"
WARN	潛在問題	"API回應時間超過2秒"
ERROR	錯誤事件	"資料庫連線失敗"
CRITICAL	系統危機	"資料外洩嘗試偵測"
必須記錄的事件:

身分驗證事件
登入成功/失敗(含IP、時間)
登出
MFA驗證結果
權限變更
資料存取事件
案件檢視(誰、何時、哪個案件)
文件下載
敏感資料查詢
AI操作事件
Prompt內容(去識別化後)
Token使用量
模型回應(完整或摘要)
使用者對AI輸出的回饋(讚/倒讚)
系統變更事件
設定變更
軟體部署
資料庫Schema變更
日誌格式 (JSON):

json

Copy
{
  "timestamp": "2024-05-25T14:32:15.123Z",
  "level": "INFO",
  "service": "inference-service",
  "trace_id": "a1b2c3d4-e5f6-7890",
  "user": {
    "id": "reviewer_001",
    "name": "張審查員",
    "role": "senior_reviewer",
    "ip": "192.168.1.100"
  },
  "action": "ai_query",
  "resource": {
    "type": "case",
    "id": "MD-2024-12345"
  },
  "details": {
    "query": "請摘要測試報告結論",  // 已去識別化
    "model": "gpt-4o",
    "tokens_used": 3240,
    "response_time_ms": 2340,
    "result": "success"
  }
}
日誌保留政策:

一般日誌
安全相關日誌
稽核追蹤日誌
(壓縮歸檔)
6.3.2 異常偵測 (Anomaly Detection)
1. 基於規則的偵測

異常行為	偵測規則	回應動作
大量資料下載	單一使用者1小時內下載>50份文件	暫時鎖定帳號+通知資安團隊
高頻率AI查詢	單一使用者1分鐘內>20次AI請求	Rate Limiting+告警
異地登入	從非台灣IP登入	要求額外MFA驗證
離職人員存取	已停用帳號嘗試登入	立即告警+記錄
權限異常	一般審查員存取非分派案件	拒絕存取+通知主管
2. 基於機器學習的偵測

使用<span style="color:coral">Isolation Forest演算法</span>偵測異常模式:

python

Copy
from sklearn.ensemble import IsolationForest

# 特徵工程
features = [
    '每日登入次數',
    '平均session時長',
    'AI查詢次數',
    '文件下載數',
    '操作時段分布'
]

# 訓練模型(使用歷史正常行為數據)
model = IsolationForest(contamination=0.01)  # 預期1%為異常
model.fit(歷史正常行為數據)

# 即時偵測
當前行為 = extract_features(使用者當日操作)
異常分數 = model.score_samples([當前行為])[0]

if 異常分數 < 閾值:
    trigger_alert("偵測到異常行為", 使用者ID, 異常分數)
3. 告警機制


Copy
偵測到異常
    ↓
[嚴重度評估]
    ├─ 低 → Email通知
    ├─ 中 → Email + 站內通知 + 記錄儀表板
    └─ 高 → 上述 + SMS + 自動回應(如鎖定帳號)
6.3.3 模型監控 (Model Monitoring)
監控指標:

效能指標
平均回應時間
P95/P99回應時間
錯誤率
品質指標
使用者滿意度(讚/倒讚比例)
人工修正率
引用正確率
漂移偵測 (Drift Detection)
資料漂移 (Data Drift):

python

Copy
from scipy.stats import ks_2samp

# 比較訓練集與生產環境的資料分布
訓練集特徵分布 = load_training_features()
生產環境特徵分布 = get_production_features(last_30_days)

# Kolmogorov-Smirnov檢定
statistic, p_value = ks_2samp(訓練集特徵分布, 生產環境特徵分布)

if p_value < 0.05:
    alert("偵測到資料漂移,建議重新訓練模型")
概念漂移 (Concept Drift):
監控模型預測與實際結果的差異趨勢。

4. 有害內容偵測

使用<span style="color:coral">Azure Content Safety API</span>或自建分類器偵測:

仇恨言論
暴力內容
性相關內容
自我傷害內容
python

Copy
def check_content_safety(text):
    response = content_safety_client.analyze_text(
        text=text,
        categories=["Hate", "Violence", "Sexual", "SelfHarm"]
    )
    
    for category in response.categories:
        if category.severity >= 4:  # 0-6級,4以上視為高風險
            return False, f"偵測到不當內容: {category.category}"
    
    return True, "內容安全"
6.4 合規性框架 (Compliance Framework)
適用法規清單
法規	適用範圍	合規要點
<span style="color:coral">個人資料保護法</span>	個資處理	去識別化、當事人同意、資料最小化
<span style="color:coral">政府資訊安全管理規範</span>	政府機關資訊系統	分級管控、定期稽核
<span style="color:coral">醫療器材管理法</span>	審查流程	程序合法性、決策可追溯
<span style="color:coral">行政程序法</span>	行政決策	說明理由、給予陳述機會
GDPR精神遵循
雖非歐盟管轄,但參考GDPR最佳實踐:

資料可攜權
使用者可匯出自己的查詢歷史與偏好設定
被遺忘權
案件結案後可申請刪除非必要留存資料
資料處理透明
明確告知AI如何處理資料
提供AI決策解釋
隱私設計 (Privacy by Design)
系統設計階段即納入隱私保護
7. 基礎設施與部署 (Infrastructure & Deployment)
7.1 Kubernetes叢集規劃
建議採用 <span style="color:coral">GKE (Google Kubernetes Engine)</span> 或 <span style="color:coral">AKS (Azure Kubernetes Service)</span> 搭配地端節點(<span style="color:coral">Anthos/Azure Arc</span>)實現混合雲。

Node Pools設計
1. System Pool (系統池)

用途: 運行Kubernetes系統元件
節點規格:
VM Size: Standard_D2s_v5 (2 vCPU, 8GB RAM)
節點數: 3 (高可用)
元件:
API Gateway (Kong/Nginx Ingress)
Monitoring Agent (Prometheus/Grafana)
Log Collector (Fluent Bit)
2. App Pool (應用池)

用途: 運行Web UI與Backend API
節點規格:
VM Size: Standard_D4s_v5 (4 vCPU, 16GB RAM)
節點數: 3-10 (Auto-scaling)
Auto-scaling規則:
yaml

Copy
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-server-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api-server
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
3. AI Pool (AI運算池)

用途: 運行OCR、Embedding、本地LLM推理
節點規格:
VM Size: Standard_NC4as_T4_v3 (4 vCPU, 28GB RAM, 1x NVIDIA T4)
節點數: 2-5 (依工作負載)
GPU共享:
yaml

Copy
resources:
  limits:
    nvidia.com/gpu: 1  # 完整GPU
    # 或使用MIG切割
    nvidia.com/mig-1g.5gb: 1  # 1/7 A100
4. Vector DB Pool (向量資料庫池)

用途: 運行Qdrant/Milvus
節點規格:
VM Size: Standard_E8s_v5 (8 vCPU, 64GB RAM)
儲存: Premium SSD (低延遲)
節點數: 3 (Qdrant集群)
網路架構

Copy
[Internet]
    ↓
[Azure Front Door / Cloud CDN]
    ↓ (DDoS防護)
[Application Gateway]
    ↓ (WAF)
[Kubernetes Ingress]
    ↓
[Service Mesh - Istio]
    ├─ API Service
    ├─ AI Service
    └─ Vector DB Service
Service Mesh優勢:

自動mTLS加密
流量控制(金絲雀部署)
可觀測性(分散式追蹤)
7.2 基礎設施即程式碼 (Infrastructure as Code)
Terraform範例 - Azure Kubernetes Service
hcl

Copy
# 定義Resource Group
resource "azurerm_resource_group" "tfda_rg" {
  name     = "tfda-smartreview-rg"
  location = "Southeast Asia"  # 新加坡
  
  tags = {
    Environment = "Production"
    Project     = "Smart Review System"
  }
}

# 定義Azure Kubernetes Service
resource "azurerm_kubernetes_cluster" "tfda_aks" {
  name                = "tfda-aks-prod"
  location            = azurerm_resource_group.tfda_rg.location
  resource_group_name = azurerm_resource_group.tfda_rg.name
  dns_prefix          = "tfda-aks"
  
  # 預設Node Pool (System Pool)
  default_node_pool {
    name       = "system"
    node_count = 3
    vm_size    = "Standard_D2s_v5"
    
    upgrade_settings {
      max_surge = "33%"
    }
  }
  
  # 身分識別設定
  identity {
    type = "SystemAssigned"
  }
  
  # 網路設定
  network_profile {
    network_plugin    = "azure"
    load_balancer_sku = "standard"
    outbound_type     = "loadBalancer"
  }
  
  # 啟用監控
  oms_agent {
    log_analytics_workspace_id = azurerm_log_analytics_workspace.tfda_logs.id
  }
}

# GPU Node Pool (AI運算)
resource "azurerm_kubernetes_cluster_node_pool" "gpu_pool" {
  name                  = "gpunodes"
  kubernetes_cluster_id = azurerm_kubernetes_cluster.tfda_aks.id
  vm_size               = "Standard_NC4as_T4_v3"
  node_count            = 2
  
  # 僅允許特定工作負載排程至此
  node_taints = ["sku=gpu:NoSchedule"]
  
  node_labels = {
    "workload" = "ai-inference"
  }
}

# Vector DB Node Pool (高記憶體)
resource "azurerm_kubernetes_cluster_node_pool" "vectordb_pool" {
  name                  = "vectordb"
  kubernetes_cluster_id = azurerm_kubernetes_cluster.tfda_aks.id
  vm_size               = "Standard_E8s_v5"
  node_count            = 3
  
  node_labels = {
    "workload" = "vector-database"
  }
}

# 定義Log Analytics Workspace
resource "azurerm_log_analytics_workspace" "tfda_logs" {
  name                = "tfda-logs"
  location            = azurerm_resource_group.tfda_rg.location
  resource_group_name = azurerm_resource_group.tfda_rg.name
  sku                 = "PerGB2018"
  retention_in_days   = 90
}

# 定義Azure Container Registry
resource "azurerm_container_registry" "tfda_acr" {
  name                = "tfdaacr"
  resource_group_name = azurerm_resource_group.tfda_rg.name
  location            = azurerm_resource_group.tfda_rg.location
  sku                 = "Premium"  # 支援geo-replication
  admin_enabled       = false
  
  georeplications {
    location = "East Asia"  # 香港備份
    tags     = {}
  }
}

# 連接ACR與AKS
resource "azurerm_role_assignment" "aks_acr_pull" {
  scope                = azurerm_container_registry.tf
