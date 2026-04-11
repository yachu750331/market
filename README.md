# 美股戰情儀表板 Market War Room

即時追蹤美股市場關鍵風險指標的互動式儀表板，基於「大叔美股筆記」的底部判斷框架。

## 線上瀏覽
- **互動版**：[index.html](./index.html) — 含分頁切換、點擊展開驗證理由
- **靜態版**：[static.html](./static.html) — 一頁式總覽，適合列印或截圖

## 監控指標（22 項）

| 類別 | 指標 |
|------|------|
| 主要指數 | S&P 500、NASDAQ、費城半導體 SOX |
| 估值 | 巴菲特指標、席勒 CAPE |
| 恐慌情緒 | VIX、CNN 恐懼與貪婪、Put/Call Ratio |
| 流動性 | SOFR-IORB、ON RRP、TGA、SRF |
| 利率 | 美債 10Y/2Y、2s10s 利差、通膨預期、MOVE Index |
| 信用 | 高收益債 OAS、彭博金融狀況 FCI |
| 商品避險 | 黃金、WTI 原油、布蘭特原油、美元 DXY |

## 底部量化驗證（8 項，7 天滾動窗口）
每日收集數據，基於過去 7 個交易日判斷是否滿足底部條件。
詳見 `data/bottom_checklist_YYYY-MM-DD.json`。

## 更新頻率
- **每日**：指數、VIX、利率、原油、黃金、DXY、恐懼貪婪、Put/Call、HY Spread
- **每週**：流動性（SOFR、ON RRP、TGA、SRF）、彭博 FCI
- **每月**：巴菲特指標、CAPE

## ⚠ 免責聲明
本儀表板僅供研究參考，非投資建議。數據來自公開市場來源，非即時報價。
