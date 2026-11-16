# 《末日大逃殺》自訂地圖

三張可匯入現有地圖編輯器的定稿地圖，需搭配支援自訂地圖的《末日大逃殺》遊戲版本使用。

| 難度 | 地圖 | 定稿來源 |
|---|---|---|
| Easy | [easy.json](maps/easy.json) |
| Medium | [medium.json](maps/medium.json) |
| Hard | [hard.json](maps/hard.json) |

## 安裝及切換

將 `maps/` 的三個 JSON 複製到遊戲的 `Apocalypse_Data/StreamingAssets/maps/`；Unity 專案則放在 `game/unity/Assets/StreamingAssets/maps/`。啟動時可選擇：

```powershell
.\Apocalypse.exe -map easy.json
.\Apocalypse.exe -map medium.json
.\Apocalypse.exe -map hard.json
```

或在 `StreamingAssets/config.json` 設定 `"mapFile": "medium.json"`。遊戲找不到檔案或驗證失敗時可能回退至隨機地圖；請查看遊戲日誌確認實際載入的地圖。亦可透過專案內 `game/editor/map_editor.html` 匯入 JSON。

## Bot 測試結果

每組使用 12 人、相同 20 個種子 `1001–1020`。表格列成功起飛場數；結果來自 SDK 本機模擬，並非真人遊玩：

| 地圖 | 12 個 Starter | 11 種 Bot + Starter，關閉 OR-Tools | 相同隊伍，開啟 OR-Tools |
|---|---:|---:|---:|
| Easy V2 | 13/20 | 20/20 | 20/20 |
| Medium | 9/20 | 18/20 | 20/20 |
| Hard V3，平滑河道前 | 7/20 | 1/20 | 19/20 |

Easy 的 Starter 有 6 場團滅、1 場修理超時；Medium 的 Starter 9/20 起飛；Hard 的 Starter 有 13 場團滅，關閉求解器有 19 場修理超時。統計包含失敗局；完成時間只統計實際完成該階段的局數。每種地圖的逐場結果在 [`tests/`](tests/)。


SDK 在 Python 策略運算時會暫停模擬，並把實測規劃時間加 0.15 秒作為計畫提交延遲；不是正常 1 倍速。另以 Unity C# 牆鐘 1 倍速、種子 2001 各測一場：Easy 的 Starter 失敗、關閉與開啟組分別撤離 11、12 人；Medium 分別為失敗、11 人、12 人；Hard 平滑前 V3 分別為失敗、失敗、10 人。每組只有一場，不能用來估計長期勝率；原始紀錄在 [`tests/unity_1x/`](tests/unity_1x/)。真人操作與平衡仍需實際遊玩。

## 使用的 Bot 與原始碼

- **Starter：**12 人均使用遊戲 SDK 的 [starter_bot.py](https://github.com/Neoxy-6/Apocalypse/blob/f4643792dafca59cfe567f82bba1af69f61d1546/game/unity/Assets/StreamingAssets/sdk/starter_bot.py)。
- **開啟 OR-Tools：**下列 11 個新版 Bot 各一名，加一名 Starter；使用真正的 OR-Tools CP-SAT、Routing 或混合求解。
- **關閉 OR-Tools：**同一組 11 個 Bot 加一名 Starter；僅在測試程序中將 `solve_cp()` 與 `solve_routing()` 替換成各 Bot 原有的 `seed_order()`，沒有修改 Bot 原始檔。不同隊伍與 Starter 的差異不能全部歸因於 OR-Tools。

| Bot | 原始碼 |
|---|---|
| 套件架構師 | [bot_01_set_architect.py](https://github.com/YTolemop/Apocalypse_ai-bot/blob/5d8aae0900f233a1995b0340997e8bdefef78f18/bot_01_set_architect.py) |
| 期限精算師 | [bot_02_deadline_accountant.py](https://github.com/YTolemop/Apocalypse_ai-bot/blob/5d8aae0900f233a1995b0340997e8bdefef78f18/bot_02_deadline_accountant.py) |
| 產線統籌師 | [bot_03_flow_controller.py](https://github.com/YTolemop/Apocalypse_ai-bot/blob/5d8aae0900f233a1995b0340997e8bdefef78f18/bot_03_flow_controller.py) |
| 救援配置師 | [bot_04_rescue_optimizer.py](https://github.com/YTolemop/Apocalypse_ai-bot/blob/5d8aae0900f233a1995b0340997e8bdefef78f18/bot_04_rescue_optimizer.py) |
| 環線集運師 | [bot_05_milk_run.py](https://github.com/YTolemop/Apocalypse_ai-bot/blob/5d8aae0900f233a1995b0340997e8bdefef78f18/bot_05_milk_run.py) |
| 加工接力師 | [bot_06_factory_courier.py](https://github.com/YTolemop/Apocalypse_ai-bot/blob/5d8aae0900f233a1995b0340997e8bdefef78f18/bot_06_factory_courier.py) |
| 巡邏時窗師 | [bot_07_safe_window.py](https://github.com/YTolemop/Apocalypse_ai-bot/blob/5d8aae0900f233a1995b0340997e8bdefef78f18/bot_07_safe_window.py) |
| 輕載速運師 | [bot_08_light_express.py](https://github.com/YTolemop/Apocalypse_ai-bot/blob/5d8aae0900f233a1995b0340997e8bdefef78f18/bot_08_light_express.py) |
| 跨河套件師 | [bot_09_cross_river.py](https://github.com/YTolemop/Apocalypse_ai-bot/blob/5d8aae0900f233a1995b0340997e8bdefef78f18/bot_09_cross_river.py) |
| 武裝調度師 | [bot_10_armed_scheduler.py](https://github.com/YTolemop/Apocalypse_ai-bot/blob/5d8aae0900f233a1995b0340997e8bdefef78f18/bot_10_armed_scheduler.py) |
| 主線總調度 | [bot_11_mission_director.py](https://github.com/YTolemop/Apocalypse_ai-bot/blob/5d8aae0900f233a1995b0340997e8bdefef78f18/bot_11_mission_director.py) |

上述連結固定到測試所用的版本，11 個 Bot 與 Starter 均已逐檔比對內容雜湊。它們需搭配遊戲的 `apocalypse_sdk.py` 與相容的 Python 環境；測試環境使用 OR-Tools `9.15.6755`。本倉庫地圖及測試檔的 SHA-256 見 [SHA256.json](SHA256.json)。
