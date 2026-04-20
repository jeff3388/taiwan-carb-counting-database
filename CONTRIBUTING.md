# 貢獻指南 Contributing Guide

感謝你有興趣為台灣碳水計算資料庫做出貢獻。

## 資料標準

### 每筆資料必須有：
- **官方來源**：衛福部食藥署台灣食品營養成分資料庫（TFND）、衛福部每日飲食指南，或 PMID 可查詢的同儕審閱文獻
- **正確的衛福部換算份數**：依據 2018 年版飲食指南標準
- **GI 數據**：引用 Atkinson et al. (2008) International Tables of GI and GL，或其他 PMID 可查詢來源
- **GL 計算驗證**：GL = GI × net_carbs_g / 100，請自行驗算

### 資料格式
所有檔案遵循 `data/schema.json` 定義。提交前請驗證 JSON 格式正確（可使用 `jsonlint`）。

```bash
# 驗證 JSON 格式
npx jsonlint data/staples.json
```

## 歡迎的貢獻類型

- 新增台灣常見食物（含衛福部 TFND 來源）
- 更正 GI/GL 數值（需附新文獻 PMID）
- 新增食物類別（如台灣夜市小吃、超商食品）
- 翻譯改善（中英文描述）
- 補充 `notes` 欄位的實用資訊（烹調方式影響、台灣在地飲食習慣）

## 不接受的貢獻

以下 PR 將直接關閉，不做審查：

- ❌ 沒有官方來源的數值（不接受「我查網路說是 XX」）
- ❌ 廠商提供的自家產品營養數據（利益衝突，需第三方獨立數據）
- ❌ 含有外部商業連結的 notes（本庫不是廣告平台）
- ❌ 推廣特定補充品、保健食品或飲食法的內容
- ❌ 未符合 schema.json 格式的提交

## Pull Request 流程

1. Fork 此 repo
2. 建立功能分支：`git checkout -b add/food-name`
3. 修改資料並確認 JSON 格式正確
4. PR 說明中需列出：食物名稱、資料來源（TFND 項目名稱或 PMID）、GI 文獻出處
5. 等待維護者審查（通常 3–7 個工作天）

## 資料來源參考

- 衛福部食藥署 TFND：https://consumer.fda.gov.tw/Food/TFND.aspx
- 衛福部每日飲食指南（2018）：https://www.hpa.gov.tw/Pages/Detail.aspx?nodeid=544&pid=725
- International GI Tables：Atkinson FS, Foster-Powell K, Brand-Miller JC. (2008). Diabetes Care, 31(12), 2281–2283. PMID: 18835944

## 行為準則

請遵守友善、尊重的溝通方式。本庫的目標是為台灣民眾提供準確的飲食資訊，所有討論應以資料正確性為核心。
