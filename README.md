# HIS Tools - 學生成績記錄

一個帶密碼保護的學生成績記錄系統，用於追蹤 Cici 和 Coco 的練習、測驗及考試成績。

## 功能

- 🔒 **密碼保護** - 輸入密碼才可查看成績
- 📊 **科目分佈圖** - Pie Chart 顯示各科記錄數量
- 📈 **年級平均分圖** - Bar Chart 按小一至小六順序顯示平均分
- 👧 **學生篩選** - 全部 / Cici / Coco
- 📚 **科目篩選** - 中文 / 英文 / 數學 / 中文常識 / 英文常識
- 📋 **成績記錄列表** - 按日期排序，顯示分數、年級、出處、學習重點
- 📝 **詳情查看** - 錯誤重點、需複習重點、試卷/批改卷連結
- 🎨 **分數顏色** - 85%以上綠色、70-84%黃色、70%以下紅色

## ADMIN 使用說明

### 日常運作
1. 數據來源：飛書多維表格「試卷記錄」
2. 更新數據：呼叫 J00 重新生成 `student-records.json` 並上傳
3. 密碼修改：在常數設定表 J06 的 Parameter 中修改 `password`，然後更新 HTML 中的 PASSWORD 常量

### 新增成績記錄
1. 在飛書多維表格新增記錄
2. 呼叫 J00 重新生成 JSON
3. HTML 會自動讀取最新 JSON

### 調整參數
- 密碼：J06 Parameter → `password`
- 版本號：J06 Parameter → `version`
- 數據源：J06 Parameter → `base_token`, `table_id`

## Logic Flow

```
用戶打開頁面 → 輸入密碼 → 驗證密碼
    ↓
載入 student-records.json
    ↓
渲染統計圖表（Pie Chart + Bar Chart）
    ↓
渲染成績記錄列表
    ↓
用戶篩選（學生/科目）→ 重新渲染圖表和列表
    ↓
點擊記錄 → 顯示詳情彈窗
```

## Business Flow

```
飛書多維表格（試卷記錄）
    ↓ J00 定時/手動觸發
生成 student-records.json
    ↓
上傳到 GitHub
    ↓
HTML 動態讀取 JSON
    ↓
用戶查看成績
```

## JSON Structure

```json
{
  "generatedAt": "2026-10-09 22:00:00",
  "totalRecords": 31,
  "students": ["Cici", "Coco"],
  "subjects": ["中文", "英文", "數學", "中文常識", "英文常識"],
  "records": [
    {
      "id": "20261004006",
      "student": "Cici",
      "subject": "數學",
      "grade": "小三",
      "source": "青松侯寶垣小學",
      "date": "2025-11-10",
      "score": 81.0,
      "studyFocus": "學習重點...",
      "mistakeFocus": "錯誤重點...",
      "reviewFocus": "需複習重點...",
      "paperUrl": "https://...",
      "paperText": "試卷",
      "correctedUrl": "https://...",
      "correctedText": "批改卷"
    }
  ]
}
```

### 字段說明

| 字段 | 類型 | 說明 |
|------|------|------|
| id | string | 記錄ID |
| student | string | 學生名（Cici/Coco） |
| subject | string | 科目 |
| grade | string | 年級（小一至小六） |
| source | string | 試卷出處 |
| date | string | 測驗日期（YYYY-MM-DD） |
| score | number/null | 成績百分比（null=未評分） |
| studyFocus | string | 針對的學習重點 |
| mistakeFocus | string | 學生錯誤重點 |
| reviewFocus | string | 需複習學習重點 |
| paperUrl | string | 試卷文件連結 |
| paperText | string | 試卷文件顯示文字 |
| correctedUrl | string | 批改卷文件連結 |
| correctedText | string | 批改卷文件顯示文字 |

## Version Control

| 版本 | 日期 | 改動 |
|------|------|------|
| v1.00 | 2026-10-09 | 初始版本，密碼保護、Pie Chart、Bar Chart、學生/科目篩選、詳情查看 |

## 相關連結

- [HIS Tools 主頁](https://hkharrycheng.github.io/his-tools/)
- [學生成績記錄](https://hkharrycheng.github.io/student-record/)
- [飛書多維表格](https://www.doubao.com/base/NdzFboXjTajV9Zsd4Fxcdhn0nHF)
- [GitHub Repository](https://github.com/hkharrycheng/student-record)