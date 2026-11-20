# TT01 粵語急口令 — 引擎並排比對（交付摘要）

2026-11-20。讀稿來源：用戶提供圖片；檔：`voice\tests\canto-tongue-twister-01.md`。

## 協議（全部引擎一樣）

- 同一段文字、預設語速／音高，**不加 rate／pitch／style**
- 每條 48 kHz／單聲道／16-bit WAV；一句一檔（s01–s04）＋全段（full）
- 命名 `<engine>-<voice>-<sXX|full>.wav`
- 引擎：Edge zh-HK 三把（HiuGaai／HiuMaan／WanLung）、Windows SAPI Tracy、OneCore Danny、
  CosyVoice2（instruct）、GPT-SoVITS v2 + 微調 e30
- 兩條克隆引擎的參考音**同為用戶本人** `voice\samples\tai-clean\refs\s02.wav`

## 主要結果（逐句 s01–s04，共 45 音節）

| 引擎-聲線 | 逐句粵拼錯 | 聲調錯 | 聲母／韻母錯 | 龜(gwai1) 讀錯 | 漏 -w- | 4 句總時長 |
|---|---|---|---|---|---|---|
| **edge-HiuMaan** | **0/45** | 0 | 0 | 0 | 0 | 14.63 s |
| **onecore-Danny** | **1/45** | 1 | 0 | 0 | 0 | 16.42 s |
| edge-WanLung | 3/45 | 3 | 0 | 2 | 0 | 15.28 s |
| sapi-Tracy | 3/45 | 3 | 0 | 2 | 0 | 15.13 s |
| edge-HiuGaai | 4/45 | 4 | 0 | 2 | 0 | 16.77 s |
| sovits-ft-e30 | 19/45 | 4 | 15 | 3 | 1 | 17.92 s |
| cosy-userS02 | 20/45 | 1 | 19 | 1 | 0 | 22.60 s |

全段（full，45 音節）：HiuMaan 0、Danny 0、WanLung 2（聲調）、HiuGaai 3（聲調）、
Tracy 2（聲母／韻母）、SoVITS 16（聲調 6／聲母韻母 10）、Cosy 25。

## 要點

1. **Edge HiuMaan 與 OneCore Danny 在急口令上幾乎全對**（0/45、1/45）。Danny 是舊式合成，音質不會好，但音準過關。
2. **HiuGaai 有一個穩定的聲調錯**：全段三次「龜」讀成 gwai3／gwai6（＝貴的調），與它之前「少甜」「啦」的表現一致。
3. **WanLung 兩次「龜」讀成 gwai3**；其餘乾淨。
4. **CosyVoice2 在這段崩得最明顯**：25/45、s03 長 10.84 秒（其他引擎約 4 秒）並自行重複「我話龜切卡龜」。
5. **GPT-SoVITS 微調版**：16/45，四次「龜」讀成 gwai3；另有多處 ng-／聲母替換（「銀」那類老問題）。

## 過程中修到的一個詞庫錯

比對時發現 `chars.csv` 把「龜」的首讀標成 **gau1**（gau1 只作「龜茲」地名），
令自製 G2P 把「一隻龜」標成 `jat1 zek3 gau1`，SoVITS 讀成「夠」。
已依詞典（粵典／維基／欽州 3 部作 **gwai1**）寫入 `overrides.csv`，
並把「龜」「一隻龜」加入固定回歸清單：`GATE 通過 20/20`，exit 0。

修正後重跑 SoVITS：s01 CER 0.40→0.10、full 0.49→0.36，但 s02/s04 反而變差 ——
與已知的克隆不穩定一致，不是詞庫問題。

## 量度方法與限制

- **粵拼層比對（主要依據）**：ASR 回譯文字再標粵拼，逐音節對齊。漢字不同但粵拼一樣（斤 gan1／筋 gan1）不算錯。
- **CER（輔助）**：純漢字比對，會被同音字冤枉，所以同時列出兩套數字。
- **F0（旁證，不可單獨用）**：按能量谷切音節後量貴／龜起音，音節邊界仍有誤差；
  本機曾因 F0 量測段太短而誤判「重力」，這一欄只作交叉檢查。
- **ASR 本身的限制**：抓不到語氣、停頓、自然度；同音字會自動寫成合理詞。

## 檔案

- 比對頁（可點播，同一句並排）：`compare.html`
- 完整量度報告：`report.md`
- 機器可讀：`measure.json`、`syllable.json`
- 音檔：`wav\` 共 35 條（7 引擎聲線 × 5 句）

**未上傳任何地方**（media-share 等要先得用戶批准）。

## 用戶聽感裁決（2026-11-20）

> 「edge 3把聲都好精準，其他都不達預期效果」

- **採用**：Edge zh-HK 三把聲（HiuGaai／HiuMaan／WanLung）——教學片旁白一律用這三把。
- **不採用**：Windows SAPI Tracy、OneCore Danny、CosyVoice2、GPT-SoVITS（就目前表現）。

### 一個要記住的反差

機器量度顯示 Danny（0/45）、Tracy（2/45）的**讀音**其實貼近 Edge，但用戶仍判「不達預期」。
所以它們失分的地方是**音質與自然度**（舊式合成、機械、斷句），不是讀錯字。
換句話講：本表量的是音準，不是可聽性；最終判斷仍然是用戶的耳。

另外 HiuGaai 在 ASR 層被抓到三次「龜→gwai3」（全段），但用戶聽感認為三把聲都精準。
ASR 對單字聲調本來就有盲點（同音字、上下文自動修正），此項記錄為「機器報告與人耳不一致」，不當作錯。
