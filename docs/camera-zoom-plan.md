# Camera Zoom 計劃（已執行）

狀態：已完成實作與基本測試

概要：
本次計劃的目標是為專案新增一組可重用且易於整合的 Camera Zoom 控制邏輯，支援：

- 限制最小/最大縮放值
- 固定步進（step）控制
- 平滑（smooth）縮放動畫（可選）
- 事件回呼 onChange
- 易於在 node / browser 環境下測試

執行內容：
1. 新增實作檔案：src/camera/zoom.js
   - 提供 ZoomController 類別
   - API: constructor(options), getZoom(), setZoom(value), zoomIn(), zoomOut(), enableSmooth(flag), onChange(callback)
   - 支援 clamp、step 與可選的平滑過度
2. 新增單元測試：test/zoom.test.js
   - 使用 node assert 進行同步/非同步基本行為驗證
3. 更新文件：本檔已更新為「已執行」狀態，並示範使用方式與注意事項

使用說明：
- Node / CommonJS：
  const ZoomController = require('./src/camera/zoom');
  const z = new ZoomController({ min: 1, max: 5, step: 0.1, initial: 1 });
  z.onChange(v => console.log('zoom:', v));
  z.zoomIn();

- 支援平滑：
  z.enableSmooth(true);
  await z.setZoom(4); // 若 smooth 開啟，setZoom 會回傳一個在動態完成後 resolve 的 Promise

設計要點與注意事項：
- setZoom 在 smooth 關閉時同步生效並回傳已設定的值（以 Promise.resolve 包裝以統一外部呼叫習慣）；在 smooth 開啟時回傳一個在動畫完成後 resolve 的 Promise。
- 所有輸入值會被 clamp 到 min/max 範圍，並以設定的 step 四捨五入到最接近的步進。
- 平滑動畫以 setInterval 實作，頻率約 60 FPS（每 16ms 更新），可透過 smoothStep 調整每一幀變化量。

後續建議：
- 若整合至實際相機硬體介面，將 onChange 與底層硬體設定綁定，並考慮取消/暫停動畫的行為。
- 若在瀏覽器中使用，可將 setInterval 換成 requestAnimationFrame 以取得更佳效能與同步顯示器更新。
- 若專案使用 TypeScript，建議新增 type definitions 或將此檔轉換為 .ts。


變更檔案：
- 新增 src/camera/zoom.js
- 新增 test/zoom.test.js
