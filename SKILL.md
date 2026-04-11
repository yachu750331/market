# 美股戰情儀表板 - Cowork 每日更新指令

## 專案概要
這是一個美股市場監控戰情儀表板，發布於 GitHub Pages。每個交易日收盤後需更新數據。

## 資料夾結構
```
G:\NAS資料\近期備份\美股訂閱群\工作\工具開發\Dashboard\
├── market/                    ← 本 repo（美股戰情儀表板）
│   ├── index.html             ← GitHub Pages 主頁（互動式 React 儀表板）
│   ├── static.html            ← 靜態總覽版（一頁式）
│   ├── data/
│   │   ├── latest.json        ← 最新一天的完整數據（被 index.html 讀取）
│   │   ├── bottom_checklist_YYYY-MM-DD.json  ← 每日底部驗證檔（累積保留）
│   │   └── archive/           ← 歷史 daily snapshot 存檔
│   ├── scripts/
│   │   └── update_helper.md   ← 數據來源與查詢方式備忘
│   ├── assets/
│   │   └── (圖片等靜態資源)
│   ├── SKILL.md               ← 本檔案（Cowork 讀取用）
│   └── README.md
├── cas/                       ← 無人機 repo（未來擴充）
└── ...
```

## 每日更新流程（交易日收盤後執行）

### Step 1：查詢最新收盤數據
使用搜尋工具或 Computer Use 開瀏覽器查詢以下數據：

**主要指數（3 項）**
| 指標 | 查詢關鍵字 | 來源 |
|------|-----------|------|
| S&P 500 | `S&P 500 closing price today` | Yahoo Finance / CNBC |
| NASDAQ Composite | `NASDAQ composite close today` | Yahoo Finance / CNBC |
| PHLX SOX (費半) | `Philadelphia semiconductor index SOX today` | Investing.com |

**恐慌情緒（3 項）**
| 指標 | 查詢關鍵字 | 來源 |
|------|-----------|------|
| VIX | `VIX index close today` | CBOE / Yahoo ^VIX |
| CNN Fear & Greed | `CNN fear and greed index` | CNN Markets |
| CBOE Put/Call Ratio | `CBOE equity put call ratio` | MacroMicro / CBOE |

**利率與債券（5 項）**
| 指標 | 查詢關鍵字 | 來源 |
|------|-----------|------|
| US 10Y Yield | `US 10 year treasury yield` | CNBC US10Y |
| US 2Y Yield | `US 2 year treasury yield` | CNBC US2Y |
| 10Y Breakeven Inflation | `10 year breakeven inflation rate` | FRED T10YIE |
| MOVE Index | `MOVE index treasury volatility` | MacroMicro |
| HY OAS Spread | `ICE BofA high yield OAS spread` | FRED BAMLH0A0HYM2 |

**避險與商品（4 項）**
| 指標 | 查詢關鍵字 | 來源 |
|------|-----------|------|
| 黃金 XAU/USD | `gold spot price today` | Kitco / GoldPrice.org |
| WTI CL1 | `WTI crude oil futures price` | OilPrice.com / CNBC |
| Brent CO1 | `Brent crude oil futures price` | Investing.com |
| DXY | `US dollar index DXY` | TradingEconomics |

**流動性（4 項）- 這些更新頻率較低，每週確認一次即可**
| 指標 | 來源 |
|------|------|
| SOFR vs IORB | FRED SOFR / NY Fed |
| ON RRP | FRED RRPONTSYD |
| TGA | Treasury General Account via FRED |
| SRF | NY Fed Standing Repo Facility |

**估值（2 項）- 月度級別，每月初更新**
| 指標 | 來源 |
|------|------|
| 巴菲特指標 | GuruFocus / CurrentMarketValuation.com |
| Shiller CAPE | multpl.com/shiller-pe |

**彭博金融狀況指數 - 每週更新**
| 指標 | 來源 |
|------|------|
| Bloomberg FCI | Bloomberg Terminal / MacroMicro |

### Step 2：更新 data/latest.json
用查到的數據更新 `data/latest.json`，格式參照現有檔案。
- `prev` 欄位填入前一天的 `value`
- `value` 欄位填入今天查到的新數據
- 流動性/估值類如果今天沒有新數據，保持不變

### Step 3：更新底部驗證檔
1. 複製最近一份 `data/bottom_checklist_YYYY-MM-DD.json`
2. 在 `daily_data` 中新增今天的條目
3. 如果超過 7 個交易日，刪除最舊的一筆
4. 重新檢查 8 項驗證條件，更新 `met` 和 `verdict`
5. 另存為 `data/bottom_checklist_今天日期.json`

### Step 4：更新 index.html 和 static.html
- 把 `latest.json` 的數據同步到兩個 HTML 檔案中的硬編碼數值
- 更新 header 的日期顯示
- 確認紅黃綠燈信號是否因數值變動需要調整

### Step 5：Git 推送
```bash
cd G:\NAS資料\近期備份\美股訂閱群\工作\工具開發\Dashboard\market
git add .
git commit -m "daily update YYYY-MM-DD"
git push
```

## 底部驗證清單判定規則

| # | 項目 | 判定邏輯（7天滾動窗口） |
|---|------|----------------------|
| 1 | 巴菲特指標 | 7天內是否曾跌至 < 160% |
| 2 | CAPE | 7天內是否曾回落至 ≤ 25x |
| 3 | VIX 底背離 | 7天內 VIX 盤中高點曾 > 40，且之後 SP500 新低時 VIX 高點下降 |
| 4 | 恐懼與貪婪 | 7天內至少 2 天 ≤ 15 |
| 5 | Put/Call Ratio | 7天內至少 2 天 ≥ 1.2 且峰值 ≥ 1.5 |
| 6 | 巨量換手+縮量止跌 | 7天內先出現 SPY > 150M 崩跌，後連續縮量（< 110M）且股價不破前低 |
| 7 | HY 利差見頂回落 | 7天內 HY OAS 從峰值回落 > 10% |
| 8 | VIX 回落 < 25 | 7天內 VIX 從 > 35 的峰值快速回落至 < 25 |

## 進度條閾值定義

每個指標的進度條左端（綠）和右端（紅）門檻定義在 `latest.json` 的 `threshold` 欄位中。
- 正向指標（越高越危險）：左=green，右=red
- 反向指標（越低越危險，invertColor=true）：左=green（安全高值），右=red（危險低值）

## 注意事項
- 美股休市日（週末、聯邦假日）不需更新
- 數據以收盤價為準，盤中數據僅用於 VIX 盤中高點
- 所有時間以美東時間（ET）為基準
- 非投資建議，僅供研究參考
