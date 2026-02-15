# Noctalia IME Indicator (A / 中)

給 Noctalia 使用的極簡輸入法指示器（fcitx5），風格參考 macOS：

- 英文狀態顯示 `A`
- 中文/注音狀態顯示 `中`
- 左鍵可直接切換中英

適用於「不想看托盤輸入法圖示、但希望在 bar 上有一個乾淨明確的狀態指示」的使用者。

## 功能特色

- 極簡字元 icon：`A` / `中`
- 高頻更新（150ms 輪詢 + 點擊後立即刷新）
- Tooltip 只顯示必要資訊：`English` / `中文/ㄅ`
- 不顯示 `Left click: ...` 這種指令提示文字
- 使用 Noctalia 樣式系統（顏色、圓角、外框、hover）

## 相容需求

- Noctalia `>= 4.0.0`
- `fcitx5`
- `fcitx5-remote`
- Wayland + Quickshell 環境（Noctalia 既有要求）

## 專案結構

```text
noctalia-ime-indicator/
├── manifest.json
├── Main.qml
├── BarWidget.qml
├── LICENSE
└── README.md
```

## 安裝方式

### 方式 A：本機安裝（最簡單）

1) 複製插件到 Noctalia 插件資料夾（資料夾名稱必須是 `ime`，且要和 manifest 的 `id` 一致）

```bash
mkdir -p ~/.config/noctalia/plugins
cp -r /path/to/noctalia-ime-indicator ~/.config/noctalia/plugins/ime
```

2) 在 `~/.config/noctalia/plugins.json` 啟用插件狀態

```json
{
  "states": {
    "ime": {
      "enabled": true,
      "sourceUrl": "local"
    }
  }
}
```

3) 在 `~/.config/noctalia/settings.json` 的 bar widgets 加入：

```json
{
  "id": "plugin:ime"
}
```

4) 重啟 Noctalia

```bash
qs kill -c noctalia-shell && qs -c noctalia-shell --daemonize
```

### 方式 B：以開發目錄 symlink 安裝（建議開發者）

```bash
mkdir -p ~/.config/noctalia/plugins
ln -s /path/to/noctalia-ime-indicator ~/.config/noctalia/plugins/ime
```

接著同樣設定 `plugins.json` 與 bar widget，重啟即可。

## 設定與客製

目前為零設定插件（`metadata.defaultSettings` 為空），行為固定如下：

- 狀態判斷：透過 `fcitx5-remote` + `fcitx5-remote -n`
- 中文判定：`bopomofo` / `Bopomofo` / `mcbopomofo`
- 切換動作：`fcitx5-remote -t`

若要改文字或判斷邏輯，直接修改 `BarWidget.qml`：

- `indicatorText`
- `tooltipTextValue`
- `Process.command`

## 開發與除錯

### 啟用熱重載（官方建議）

```bash
NOCTALIA_DEBUG=1 qs -c noctalia-shell
```

存檔後會自動重載 plugin（開發模式）。

### 常用檢查

```bash
# 檢查 plugin 狀態
cat ~/.config/noctalia/plugins.json

# 檢查 manifest
cat ~/.config/noctalia/plugins/ime/manifest.json
```

## 發布指南（重點）

根據 Noctalia 官方文件與官方插件倉庫，目前有兩條發布路線：

### 路線 1：獨立 GitHub Repo（最快）

適合先行發布、快速迭代。

1) 建立 repository（例如 `noctalia-ime-indicator`）
2) 更新 `manifest.json` 的 `repository` 指向你的 repo URL
3) 推送程式碼並打 tag/release
4) 使用者可手動 clone/copy 到 `~/.config/noctalia/plugins/ime`

### 路線 2：提交到官方主插件倉庫（可被主 registry 收錄）

官方主倉庫：`https://github.com/noctalia-dev/noctalia-plugins`

流程（依官方 README）：

1) Fork 官方插件倉庫
2) 在倉庫根目錄新增你的插件資料夾（例如 `ime/`）
3) 放入必要檔案：
   - `manifest.json`（必須）
   - 至少一個 entry point（本插件為 `barWidget`）
   - `README.md`（建議）
   - `preview.png`（建議，官方建議 16:9、960x540）
4) 發 PR
5) 官方 CI 會自動更新 `registry.json`（不需要手動改）

## 發布前檢查清單

- `manifest.json` 合法 JSON
- `id` 與資料夾名稱一致（本插件為 `ime`）
- 版本號符合語意化版本（SemVer，例如 `0.2.0`）
- `minNoctaliaVersion` 設定正確
- `repository` 指向正確公開網址
- 本機測試：可啟用、可加入 bar、可切換中英

## 建議版本策略

- `PATCH`：修 bug（例如 tooltip 文案修正）
- `MINOR`：新增功能（例如加入可選樣式）
- `MAJOR`：破壞性修改（例如重做設定格式）

## 授權

MIT

## 參考資料

- Noctalia 插件開發總覽：
  - https://docs.noctalia.dev/development/plugins/overview/
- Getting Started：
  - https://docs.noctalia.dev/development/plugins/getting-started/
- Manifest 參考：
  - https://docs.noctalia.dev/development/plugins/manifest/
- Bar Widget 參考：
  - https://docs.noctalia.dev/development/plugins/bar-widget/
- 官方插件倉庫：
  - https://github.com/noctalia-dev/noctalia-plugins
