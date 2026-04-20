# 台灣碳水計算資料庫（Taiwan Carbohydrate Counting Database）

以機器可讀格式整理台灣常見食物的碳水化合物含量，採用衛生福利部飲食指南「份」制單位，同時提供淨碳水（扣除膳食纖維）、GI 值與交換份數，方便糖尿病患者、低碳飲食者與 App 開發者使用。

---

## 為什麼建立這個

台灣糖尿病盛行率約 11%（逾 230 萬人），另有約 15% 處於糖前期。碳水計算是血糖管理的核心技能，但現有資料存在幾個問題：

- USDA FoodData Central 沒有台灣在地食物（手搖飲、便當主食、夜市食物）
- TFND 雖有完整數據，但格式為網頁查詢，無法直接嵌入 App
- 台灣衛福部「飲食指南」的「份」制換算表散落在 PDF 指引中，缺乏機器可讀版本
- 「總碳水」與「淨碳水（扣纖維）」的區別在台灣健康內容中很少被清楚說明

本資料庫：
1. 整合 TFND 數值，提供台灣食物的機器可讀碳水資料
2. 以衛福部「份」制換算（1 份主食 = 15g 碳水 = 70 kcal）
3. 標注淨碳水（`net_carbs_g`），方便低碳/生酮飲食計算
4. 連結 GI 值，讓血糖管理有完整的量與質資訊

---

## 資料檔案

| 檔案 | 說明 |
|------|------|
| [`data/staples.json`](data/staples.json) | 主食類（米飯、麵食、根莖類、早餐主食） |
| [`data/fruits.json`](data/fruits.json) | 水果類（台灣常見 + 衛福部水果份換算）|
| [`data/legumes-dairy.json`](data/legumes-dairy.json) | 豆類與乳品（低 GI 碳水來源）|
| [`data/taiwan-exchange-guide.json`](data/taiwan-exchange-guide.json) | 衛福部六大類食物「份」換算說明 |
| [`data/schema.json`](data/schema.json) | JSON Schema 欄位定義 |

---

## 核心概念

### 台灣衛福部「份」制

衛福部飲食指南以「份」為計量單位：

| 食物類別 | 1 份含碳水 | 1 份含熱量 | 範例 |
|---------|-----------|-----------|------|
| 全穀雜糧類 | 15g | 70 kcal | 白飯 1/4 碗（50g） |
| 水果類 | 15g | 60 kcal | 蘋果 1 顆小（130g） |
| 乳品類 | 12g | 120–150 kcal | 全脂牛奶 240ml |
| 蔬菜類 | 5g | 25 kcal | 生菜 1 碗（100g） |

### 淨碳水 vs. 總碳水

```
淨碳水 (net_carbs_g) = 總碳水 (total_carbs_g) - 膳食纖維 (fiber_g)
```

- **低碳 / 生酮飲食**：計算淨碳水（膳食纖維不影響血糖）
- **糖尿病碳水計算**：兩種方式均有使用，遵照個人醫師 / 衛教師指示

---

## 資料預覽

### 主食類碳水對照（每份）

| 食物 | 份量 | 總碳水 | 纖維 | 淨碳水 | 衛福部份數 | GI |
|------|------|--------|------|--------|-----------|-----|
| 白飯（熟） | 1/4 碗 50g | 18g | 0.2g | 17.8g | 1.2 份 | 72 |
| 糙米飯（熟） | 1/4 碗 50g | 17g | 1.0g | 16g | 1.1 份 | 55 |
| 地瓜（水煮）| 55g | 15g | 1.5g | 13.5g | 1.0 份 | 46 |
| 玉米（整根）| 65g | 15g | 1.5g | 13.5g | 1.0 份 | 48 |
| 燕麥粥 | 250ml 熟 | 18g | 2.0g | 16g | 1.2 份 | 55 |
| 白吐司 | 1片 30g | 15g | 0.8g | 14.2g | 1.0 份 | 70 |
| 芋頭（蒸）| 55g | 15g | 1.8g | 13.2g | 1.0 份 | 55 |

### 水果類碳水（1 份 = 15g 碳水）

| 水果 | 份量 | 總碳水 | 淨碳水 | GI |
|------|------|--------|--------|-----|
| 芭樂（珍珠）| 155g | 15g | 10.4g | 31 |
| 蘋果 | 130g | 15g | 12.5g | 36 |
| 西瓜 | 250g | 15g | 14.5g | 76 |
| 香蕉（熟透）| 70g | 15g | 13.1g | 62 |

---

## 使用範例

```javascript
import staples from './data/staples.json'

// 計算一餐的碳水攝取
function mealCarbCount(items) {
  return items.reduce((total, item) => {
    const food = staples.find(f => f.id === item.id)
    if (!food) return total
    const servings = item.grams / food.serving_g
    return total + (food.net_carbs_g * servings)
  }, 0)
}

// 範例：糙米飯 150g + 地瓜 100g
const carbs = mealCarbCount([
  { id: 'brown-rice-cooked', grams: 150 },
  { id: 'sweet-potato-boiled', grams: 100 }
])
// 糙米: 17g × (150/50) = 51g；地瓜: 13.5g × (100/55) = 24.5g → 合計 ~75.5g 淨碳水
```

```python
import json

with open('data/staples.json') as f:
    staples = json.load(f)

# 找出所有淨碳水 < 20g/份 的主食（低碳選擇）
low_carb = [f for f in staples if f['net_carbs_g'] < 20]
for f in sorted(low_carb, key=lambda x: x['net_carbs_g']):
    print(f"{f['name_zh']}: {f['net_carbs_g']}g 淨碳水 / {f['serving_g']}g")
```

---

## 資料來源

| 來源 | 用途 |
|------|------|
| 衛生福利部食品藥物管理署 — 台灣食品營養成分資料庫（TFND）| 所有碳水 / 纖維數值基礎 |
| 衛生福利部 — 每日飲食指南（2018 修訂版）| 「份」制換算標準 |
| 衛生福利部 — 國人膳食營養素參考攝取量（DRIs）第八版 | 每日碳水攝取建議範圍 |
| Atkinson FS et al. (2008). International Tables of Glycemic Index and Glycemic Load Values. *Diabetes Care*, 31(12):2281–2283 | GI 值來源 |

---

## 健康聲明

**本資料庫僅供教育性與研究性用途，不構成醫療或飲食建議。糖尿病患者的碳水計算目標因個人胰島素敏感度、藥物使用、血糖控制目標而不同，請依照糖尿病衛教師或醫師的個人化建議進行碳水計算，不應以本資料庫自行調整藥物劑量。**

---

## 相關資源

- [taiwan-glycemic-index-database](https://github.com/twjojo/taiwan-glycemic-index-database) — 台灣食物 GI / GL 資料集（本資料庫 GI 值來源）
- [碳水計算入門與台灣食物 GI 完整說明：metabolab.tw](https://metabolab.tw/articles/low-gi-diet-blood-sugar-control)

---

## 授權

[![CC BY 4.0](https://licensebuttons.net/l/by/4.0/88x31.png)](https://creativecommons.org/licenses/by/4.0/)

[Creative Commons 姓名標示 4.0 國際授權](https://creativecommons.org/licenses/by/4.0/deed.zh_TW)  
引用格式：`Taiwan Carbohydrate Counting Database, github.com/twjojo/taiwan-carb-counting-database`
