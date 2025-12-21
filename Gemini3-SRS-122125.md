AI 驅動醫療器材追蹤追溯系統 (AI-MDTTS) 技術規格說明書
版本： 2.5
專案名稱： AI-driven Medical Device Track & Trace System
核心框架： React 19 + TypeScript + Google Gemini API
目標讀者： 前端開發初學者、醫療資訊專案經理、系統分析師
壹、 專案概述與設計理念
1.1 專案背景
在醫療器材管理中，數據的準確性與即時性至關重要。傳統的追溯系統往往依賴後端龐大的伺服器運算，這不僅增加了資安風險（數據傳輸過程），也提高了建置成本。
AI-MDTTS v2.5 採用了革命性的「邊緣原生（Edge-Native）」架構，利用 Google 最新的 Gemini AI 模型，直接在使用者的瀏覽器端進行數據分析。這意味著醫療數據不需要離開使用者的電腦，極大化了隱私保護，同時利用 React 19 帶來流暢的操作體驗。
1.2 核心技術名詞解釋 (初學者專區)
React 19：Meta 開發的最新版前端框架，用於構建使用者介面。它像樂高積木一樣，讓我們用「組件 (Components)」堆疊出網頁。
TypeScript：JavaScript 的進階版，強制要求定義資料類型（例如：規定「數量」欄位必須是數字）。這在醫療系統中非常重要，可以避免很多低級錯誤。
Google Gemini API：Google 的生成式 AI 大腦。我們透過程式碼與它對話，請它幫我們分析數據或整理筆記。
SPA (Single Page Application)：單頁式應用程式。網站不會因為換頁而整頁重新整理，操作起來像手機 App 一樣順暢。
貳、 系統架構詳解 (System Architecture)
本系統採用 「用戶端代理架構 (Client-Side Agentic Architecture)」。這聽起來很複雜，但簡單來說，就是我們把 AI 當作一個住在瀏覽器裡的「虛擬助理」，所有的指揮調度都在前端完成，不需要後端伺服器介入。
2.1 邊緣原生智慧架構 (Edge-Native Intelligence)
整個系統由三個主要層次組成：
A. 前端展示層 (Frontend Layer)
技術：React 19 SPA。
樣式：Tailwind CSS。
功能：這是使用者看得到的部分。我們特別設計了「高對比度」的醫療級介面，確保醫生或護理師在忙碌或光線不佳的環境下也能看清楚。
B. AI 調度層 (AI Orchestration Layer)
核心檔案：geminiService.ts
功能：這是系統的大腦中樞，利用 Google 的 @google/genai SDK（軟體開發套件）來控制 AI。它包含三條不同的運作路徑：
結構化分析引擎 (Structured Analysis Engine)：
專門負責「讀取數據」。當使用者上傳 CSV 表格時，這個引擎會把它轉成 AI 看得懂的格式，然後要求 AI 回傳標準的 JSON 格式分析報告。
對話代理人 (Conversational Agent)：
專門負責「聊天」。它會記住剛剛上傳的數據內容，使用者可以隨時問它：「這批貨有沒有異常？」，它會根據上下文回答。
筆記整理引擎 (Notekeeper Engine)：
專門負責「寫作」。它可以把雜亂的臨床筆記或會議記錄，自動整理成漂亮的 Markdown 文件。
C. 資料層 (Data Layer)
特性：零持久化 (Zero-persistence)。
解釋：為了絕對的資安，所有的醫療記錄（CSV/JSON）都只暫存在瀏覽器的「記憶體」中。一旦使用者關閉分頁或重新整理，數據就會消失。系統不會將數據儲存到任何資料庫中。
D. 安全層 (Security)
API 金鑰管理：使用 process.env.API_KEY 環境變數。
解釋：我們絕對不能把開啟 AI 大門的鑰匙（API Key）寫死在程式碼裡。我們透過環境變數的方式注入，確保金鑰不會暴露在公開的程式碼中。
2.2 組件層級結構 (Component Hierarchy)
想像這是一個樹狀圖，App.tsx 是樹根，控制著所有樹枝（功能）：
App.tsx (全域控制器)
管理整個網站的狀態，例如：現在是中文還是英文？是深色模式還是淺色模式？使用者選了哪位畫家的風格？目前載入了哪些醫療紀錄？
components/NoteKeeper.tsx (智慧筆記本)
這是一個多功能的文字處理器。使用者可以切換三種視角：
Raw：原始輸入的雜亂文字。
Source：AI 整理後的 Markdown 語法。
Rendered：最終呈現的漂亮排版。
components/DataPreview.tsx (數據預覽表)
採用專業的「鋼鐵與岩板 (Steel & Slate)」設計風格。這不只是一個表格，它能清楚展示成千上萬筆追蹤資料。
components/Visualizations.tsx (視覺化圖表)
使用 SVG 技術繪製的圖表。用來顯示「合規健康分數」，讓管理者一眼就能看出供應鏈是否健康。
components/MagicWheel.tsx (風格魔術輪)
這是一個遊戲化的介面（Gamified UI）。使用者可以轉動輪盤選擇介面風格（例如：梵谷風、賽博龐克風），讓枯燥的醫療工作增添一點樂趣。
叁、 軟體需求規格書 (SRS)
這裡是對系統功能的具體規定，分為「功能性」與「非功能性」兩大類。
3.1 功能性需求 (Functional Requirements, FR)
FR-1: 多格式資料攝取 (Multi-format Ingestion)
描述：系統必須能「吃」進兩種格式的檔案：JSON 和 CSV。這是醫療界最常見的數據交換格式。
情境：倉管人員從 ERP 系統匯出 CSV 檔，直接拖曳到網頁中即可。
FR-2: AI 生命週期分析 (AI Lifecycle Analysis)
描述：AI 必須能執行以下核心任務：
UDI 自動驗證：檢查器材的單一識別碼是否符合法規格式。
異常偵測：找出奇怪的交易，例如「迴圈交易」（A賣給B，B又賣回給A）或「數量暴增」（平常買10個，突然買1000個）。
合規評分：幫這批資料打分數（0-100分），分數越低代表風險越高。
FR-3: AI 智慧筆記本 (AI Notekeeper)
描述：這不是普通的記事本，它內建了三個由 gemini-2.5-flash 模型驅動的魔法按鈕：
文字轉 Markdown：把口語化的紀錄轉成結構化的文件。
魔法潤飾 (Magic Polish)：把寫得很爛的報告修飾成專業的醫療文件。
風險標註 (Risk Highlighting)：自動幫你把文中的「違規」或「安全隱患」用紅字標出來。
任務提取 (Task Extraction)：從落落長的文章中，自動抓出「待辦事項」。
FR-4: 資料集感知聊天 (Dataset-Aware Chat)
描述：這是一個懂你數據的聊天機器人。
情境：你上傳了 500 筆資料，然後問機器人：「幫我找出所有過期的產品。」它會直接從那 500 筆資料裡找答案，而不是講空話。
FR-5: 動態主題切換 (Dynamic Theming)
描述：支援 20 種藝術家風格的主題，並且完美支援深色/淺色模式切換，保護使用者的眼睛。
3.2 非功能性需求 (Non-Functional Requirements, NFR)
NFR-1: 易讀性 (Readability)
要求：使用 Inter 字體，並採用高對比度的排版。醫療資訊不能看錯，所以字體的清晰度比美觀更重要。
NFR-2: 隱私性 (Privacy)
要求：所有數據處理都在 Local Browser Session（瀏覽器當次工作階段）完成。伺服器端不留存任何醫療個資。
NFR-3: 響應式設計 (Responsiveness)
要求：採用流體網格佈局（Fluid Grid）。不管是用桌上型電腦，還是醫生巡房用的 iPad，介面都要能自動調整到最適合的大小。
肆、 AI 提示詞工程詳解 (AI Prompt Engineering)
要讓 AI 聽話，關鍵在於「提示詞 (Prompt)」的設計。本系統使用了精心設計的指令：
4.1 分析專用提示詞 (Analysis Prompt)
這是給「結構化分析引擎」用的指令。我們要求 AI 扮演數據分析師的角色：
指令內容（意譯）： 「請分析以下的醫療器材追蹤紀錄... 請務必只回傳 JSON 格式，內容需包含：摘要 (summary)、異常清單 (anomalies)、改進建議 (recommendations) 以及合規分數 (complianceScore)。」
初學者筆記： 強制要求 AI 回傳 JSON 格式非常重要，這樣我們的前端程式碼才能直接讀取並顯示在表格上。
4.2 筆記本專用提示詞 (Notekeeper Prompting)
針對智慧筆記本的三個魔法按鈕，我們設計了特定的指令：
潤飾 (Polish)：「請將以下醫療文件進行專業潤飾與格式化，但在語氣上要保持專業，且絕對不能修改原始的事實數據。」
風險 (Risk)：「分析以下文本，並新增一個『風險警示 (RISK ALERT)』章節，特別標註出合規性或安全性的風險點。」
任務 (Tasks)：「請從文中提取清晰的『行動項目 (ACTION ITEMS)』清單，專注於器材追蹤或合規任務。」
伍、 視覺識別設計 (Visual Identity)
5.1 鋼鐵與岩板主題 (Steel & Slate Medical Theme)
為了呈現專業與權威感，我們設計了這套預設主題：
標題列：使用 Slate-900（深岩灰色）背景搭配白色文字。深色背景能建立視覺層級的「重量感」與權威性。
狀態徽章 (Status Badges)：用顏色直觀地傳達訊息。
祖母綠 (Emerald)：代表正常、合規。
琥珀色 (Amber)：代表異常、需注意。
脈衝紅 (Pulsing Red)：代表召回、危險（加上脈衝動畫更能引起注意）。
表格行：使用 Slate-50（極淺灰）做交替色（Zebra Striping），這在長表格中能幫助視線對齊，減少看錯行的機率。
陸、 部署指南 (Deployment Instructions)
本系統推薦使用 Netlify 進行部署，因為它對 React 和環境變數的支援非常好，且適合靜態網站。
步驟 1：建置設定 (Build Configuration)
在 Netlify 的設定頁面中填入：
Build Command (建置指令): npm run build
這會把我們的 TypeScript 和 React 程式碼「編譯」成瀏覽器看得懂的 HTML/CSS/JS。
Publish Directory (發布目錄): dist
編譯好的檔案會放在這裡。
步驟 2：環境變數設定 (Environment Variables)
這是最重要的一步，為了保護 API Key：
進入 Netlify Dashboard。
找到 Site Settings > Environment Variables。
新增變數：
Key: API_KEY
Value: 你的_Google_Gemini_API_Key
步驟 3：持續部署 (Continuous Deployment)
將 Netlify 連結到你的 GitHub Repository。
設定好之後，每當你把程式碼 Push 到 GitHub，Netlify 就會自動抓取最新的程式碼，自動執行建置並更新網站。這就是所謂的 CD (Continuous Deployment)。
柒、 未來發展藍圖 (Future Roadmap)
這部分展示了 V2.5 版本之後的技術野心：
7.1 實時影像串流分析 (Live Video Streaming)
技術：Gemini Multimodal Live API。
功能：未來使用者不需要掃描槍，直接開啟手機鏡頭對準器材，AI 就能即時辨識畫面中的 UDI 條碼，甚至能辨識產品包裝是否破損。
7.2 語音轉文字筆記 (Speech-to-Text)
技術：gemini-2.5-flash-preview-tts。
功能：醫生在手術房或巡房時不方便打字。未來智慧筆記本將支援語音輸入，AI 會直接聽懂醫生的口述，並即時轉成結構化的病歷紀錄。
捌、 20 個綜合性隨訪問題 (Comprehensive Follow-up Questions)
為了幫助初學者深入思考並準備面試或進階開發，以下整理了 20 個關鍵問題：
前端架構與 React 相關
狀態管理：在 App.tsx 中管理全域狀態，如果專案變大，是否應該引入 Redux 或 Zustand？為什麼？
效能優化：React 19 的新特性（如 Server Components 或新的 Hooks）在這個純前端專案中能發揮什麼作用？
TypeScript：在定義醫療數據的 interface 時，如何處理 API 回傳可能缺漏欄位（Optional chaining）的情況？
Tailwind CSS：使用 Utility-first CSS 在維護大型專案時，會遇到什麼樣的「類名過長」問題？如何解決？
組件拆分：DataPreview.tsx 如果表格資料超過 1000 筆，如何實作分頁（Pagination）或虛擬滾動（Virtual Scrolling）來避免網頁卡頓？
AI 與 Gemini API 整合
Prompt 攻擊：如果使用者在 CSV 裡埋入惡意指令（Prompt Injection），試圖讓 AI 洩漏系統設定，該如何防禦？
Token 限制：Gemini 模型有 Context Window 限制，如果上傳的 CSV 檔案太大超過限制，前端該如何進行分塊（Chunking）處理？
錯誤處理：當 Gemini API 回傳 500 錯誤或連線逾時，前端應該如何優雅地提示使用者，而不是讓畫面白屏？
JSON 格式跑版：雖然 Prompt 要求回傳 JSON，但 AI 偶爾會回傳錯誤的格式，如何在程式碼中加入「防呆機制」或自動修復邏輯？
模型選擇：為什麼筆記功能選用 gemini-2.5-flash？在什麼情況下我們會改用更強大但更慢的 Pro 模型？
資安與部署
API Key 暴露：雖然用了環境變數，但在純前端架構下，Network Tab 還是看得到 API 請求。如何透過 Netlify Functions (Proxy) 來真正隱藏 Key？
資料殘留：雖然說是「零持久化」，但瀏覽器的 Cache 或分頁記憶體是否可能被惡意程式讀取？
合規性：這種「前端處理」架構是否符合 HIPAA 或 GDPR 對於資料處理紀錄（Audit Logs）的要求？如果不符合，該怎麼補強？
Netlify 設定：如果部署失敗，如何閱讀 Netlify 的 Deploy Log 來除錯？
產品與 UX 設計
無障礙設計 (a11y)：高對比度主題是否符合 WCAG 2.1 AA 標準？色盲使用者能分清楚紅燈和綠燈嗎？
離線使用：如果網路斷線，PWA (Progressive Web App) 技術能否讓這個系統在離線狀態下繼續運作（至少部分功能）？
使用者引導：初次使用的醫生可能不懂什麼是 CSV。如何設計一個友善的 Onboarding 流程？
魔法輪盤：遊戲化設計（Gamification）在嚴肅的醫療軟體中是否適當？使用者的接受度如何測試？
未來技術
多模態應用：在未來的「即時影像分析」中，如何處理隱私問題（例如不小心拍到病患的臉）？
邊緣運算：隨著 WebGPU 的發展，未來是否可能將 AI 模型直接下載到瀏覽器執行（如 WebLLM），完全不需要呼叫 Google API？
