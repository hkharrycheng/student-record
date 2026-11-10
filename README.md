# HIS Tools - 學生成績記錄

一個學生成績記錄系統，用於追蹤 Cici 和 Coco 的練習、測驗及考試成績。

## 功能

頁面分為三個 TAB：

- 👧 **學生圖庫** - 兩位學生上下排列，頭像取自成員表 Profile Pict（存放 `images/`）；卡片顯示中文／英文／數學／常識最新一次成績、日期與對應錯誤重點；點擊成績卡或錯誤重點可跳去「成績記錄」並篩選該生該科
- 📋 **成績記錄** - 按日期排序的記錄列表，每頁 10 行分頁，附學生／科目／年級／類型篩選
- 📊 **統計圖表** - Pie Chart 科目分佈、Bar Chart 年級平均分；點擊切片／長條會跳去「成績記錄」TAB 並帶對應篩選、清除其他維度；右上角「重置篩選」一鍵還原全部條件

其他功能：
- 👧 **學生篩選** - 全部 / Cici / Coco
- 📚 **科目篩選** - 中文 / 英文 / 數學 / 中文常識 / 英文常識
- 🏷️ **類型篩選** - 全部 / 測驗卷 / 考試卷 / 練習
- 📝 **詳情查看** - 針對重點、錯誤重點、需複習重點，以及試卷／批改卷／相關教材連結
- 🎨 **分數顏色** - 85%以上綠色、70-84%黃色、70%以下紅色

## ADMIN 使用說明

### 日常運作
1. 數據來源：飛書多維表格「試卷記錄」
2. 更新數據：呼叫 J00 重新生成 `student-records.json` 並上傳

### 新增成績記錄
1. 在飛書多維表格新增記錄
2. 呼叫 J00 重新生成 JSON
3. HTML 會自動讀取最新 JSON

### 調整參數
- 版本號：J06 Parameter → `version`
- 數據源：J06 Parameter → `base_token`, `table_id`

## Logic Flow

```
用戶打開頁面（預設「學生圖庫」TAB）
    ↓
載入 student-records.json
    ↓
圖庫：渲染頭像＋最新分科成績
記錄：渲染列表與篩選
統計：切換到該 TAB 時才渲染 Pie Chart + Bar Chart
    ↓
用戶篩選（學生/科目/年級/類型）→ 重新渲染列表與圖表
    ↓
點擊圖表切片/長條 → 跳去記錄 TAB 並帶對應篩選
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
  "version": "2026.2.01",
  "updated": "2026-11-10 20:30:00",
  "total": 52,
  "records": [
    {
      "record_id": "reczz28...",
      "student": "Cici",
      "subject": "數學",
      "grade": "小四",
      "score": 85.0,
      "date": "2026-10-23",
      "source": "青松侯寶垣小學",
      "paper_type": "測驗卷",
      "review_points": "針對的學習重點...",
      "mistakes": "學生錯誤重點...（<br>換行）",
      "focus_points": "需複習學習重點...",
      "paper_url": "https://...",
      "paper_text": "試卷標題",
      "graded_url": "https://...",
      "graded_text": "批改卷標題",
      "materials": [{ "title": "相關教材名", "url": "https://..." }]
    }
  ]
}
```

### 字段說明

| 字段 | 類型 | 說明 |
|------|------|------|
| record_id | string | 飛書記錄ID |
| student | string | 學生名（Cici/Coco） |
| subject | string | 科目 |
| grade | string | 年級（小一至小六） |
| source | string | 試卷出處（豆包AI／青松侯寶垣小學／第三方） |
| date | string | 日期（YYYY-MM-DD） |
| score | number/null | 成績百分比（null=未評分） |
| paper_type | string | 試卷類型（測驗卷／考試卷／練習） |
| review_points | string | 針對的學習重點 |
| mistakes | string | 學生錯誤重點（`<br>` 換行） |
| focus_points | string | 需複習學習重點 |
| paper_url | string | 試卷文件連結 |
| paper_text | string | 試卷標題（無標題時前端顯示「試卷」） |
| graded_url | string | 批改卷文件連結 |
| graded_text | string | 批改卷標題 |
| materials | array | 相關教材 `{title, url}`，可為空陣列 |

## Version Control

| 版本 | 日期 | 改動 |
|------|------|------|
| v1.00 | 2026-10-09 | 初始版本，密碼保護、Pie Chart、Bar Chart、學生/科目篩選、詳情查看 |
| v1.02 | 2026-11-10 | 新增試卷類型（測驗卷／考試卷／練習）標籤與篩選；詳情彈窗補相關教材連結；修正 JSON snake_case 欄位映射 |
| v1.03 | 2026-11-10 | 移除密碼登入保護與登出按鈕，打開頁面直接顯示記錄 |
| v1.04 | 2026-11-10 | 改三 TAB 結構：學生圖庫（頭像＋最新分科成績）、成績記錄、統計圖表；圖表點擊跳轉記錄並帶篩選；TAB 改遊戲風格、固定底部拍齊導航列；記錄列表每頁 10 行分頁 |
| v1.05 | 2026-11-10 | 圖庫成績卡／錯誤重點可點擊跳轉記錄並篩選該生該科；統計 TAB 新增重置篩選按鈕；圖表點擊鑽取時清除其他維度篩選 |

## 相關連結

- [HIS Tools 主頁](https://hkharrycheng.github.io/his-tools/)
- [學生成績記錄](https://hkharrycheng.github.io/student-record/)
- [飛書多維表格](https://www.doubao.com/base/NdzFboXjTajV9Zsd4Fxcdhn0nHF)
- [GitHub Repository](https://github.com/hkharrycheng/student-record)