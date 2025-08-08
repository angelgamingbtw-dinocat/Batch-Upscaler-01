<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Batch Upscaler PS99 / PSX with Controls</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap');

  * {
    box-sizing: border-box;
  }
  body {
    font-family: 'Poppins', Arial, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    margin: 0; padding: 40px 0 80px;
    display: flex; flex-direction: column;
    align-items: center; justify-content: flex-start;
    min-height: 100vh; color: #f0f0f5;
    user-select: none; overflow-y: auto;
  }
  h1 {
    margin-bottom: 15px;
    font-weight: 700;
    font-size: 2.4rem;
    letter-spacing: 1.5px;
    text-shadow: 0 3px 6px rgba(0,0,0,0.3);
    color: #f0f0f5;
  }
  .mode-toggle {
    display: flex;
    gap: 16px;
    margin-bottom: 20px;
  }
  .mode-btn {
    cursor: pointer;
    padding: 12px 30px;
    font-weight: 600;
    font-size: 1.1rem;
    border-radius: 40px;
    background: rgba(255 255 255 / 0.2);
    color: #eee;
    box-shadow: 0 6px 15px rgba(0,0,0,0.2);
    transition: background-color 0.3s ease, color 0.3s ease;
    user-select: none;
    border: none;
  }
  .mode-btn.active {
    background: #f0e130;
    color: #333;
    box-shadow: 0 10px 25px rgba(240, 225, 48, 0.8);
    font-weight: 700;
  }
  .container {
    background: #1f1f38;
    padding: 30px 40px 40px;
    border-radius: 20px;
    box-shadow:
      0 4px 15px rgba(0, 0, 0, 0.25),
      0 10px 40px rgba(118, 75, 162, 0.4);
    text-align: center;
    width: 520px;
    color: #ddd;
    display: flex;
    flex-direction: column;
  }
  input[type="file"] {
    cursor: pointer;
    font-size: 1rem;
    border: 2px dashed #6b5b95;
    background: transparent;
    padding: 14px 18px;
    border-radius: 14px;
    transition: border-color 0.3s ease;
    width: 100%;
    max-width: 480px;
    margin: 0 auto 25px;
    display: block;
    color: #ccc;
  }
  input[type="file"]:hover,
  input[type="file"]:focus {
    border-color: #a084ca;
    outline: none;
  }
  #previewContainer {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(80px, 1fr));
    gap: 12px;
    max-height: 180px;
    overflow-y: auto;
    border-radius: 14px;
    background: #292949;
    padding: 12px;
    box-shadow: inset 0 0 12px #4b3b82;
    margin-bottom: 25px;
    flex-shrink: 0;
  }
  #previewContainer canvas {
    border-radius: 14px;
    box-shadow:
      0 8px 16px rgba(0,0,0,0.3),
      inset 0 0 5px rgba(255,255,255,0.1);
    background: #36365b;
    border: 2px solid #665fa9;
    width: 80px !important;
    height: 80px !important;
    transition: transform 0.3s ease;
  }
  #previewContainer canvas:hover {
    transform: scale(1.1) rotate(1deg);
    box-shadow:
      0 12px 24px rgba(102, 126, 234, 0.8),
      inset 0 0 8px rgba(255,255,255,0.3);
  }
  .buttons {
    display: flex;
    flex-direction: column;
    gap: 12px;
    margin-top: 15px;
  }
  button {
    background: linear-gradient(135deg, #764ba2, #667eea);
    color: #fff;
    border: none;
    border-radius: 50px;
    padding: 12px 30px;
    font-size: 1rem;
    font-weight: 700;
    cursor: pointer;
    box-shadow: 0 8px 20px rgba(102, 126, 234, 0.6);
    transition: all 0.3s ease;
    width: 100%;
    max-width: 480px;
  }
  button:hover:not(:disabled) {
    background: linear-gradient(135deg, #5a3e99, #4a3290);
    box-shadow: 0 12px 30px rgba(74, 50, 144, 0.8);
  }
  button:disabled {
    background: #555;
    cursor: not-allowed;
    box-shadow: none;
  }
  #version {
    margin-top: 20px;
    font-size: 12px;
    color: #aaa;
    user-select: none;
  }
  #footer {
    margin-top: 30px;
    font-size: 14px;
    color: #bbb;
    font-weight: 600;
    user-select: none;
  }

  /* Slider container */
  .controls {
    background: #292949;
    border-radius: 14px;
    padding: 18px 25px;
    margin-bottom: 20px;
    box-shadow: inset 0 0 15px #4b3b82;
  }
  .control-group {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 14px;
  }
  .control-group label {
    font-weight: 600;
    font-size: 0.9rem;
    color: #e0e0ff;
    user-select: none;
    flex: 1 1 130px;
  }
  input[type="range"] {
    width: 65%;
    cursor: pointer;
    -webkit-appearance: none;
    height: 8px;
    border-radius: 6px;
    background: #6b5b95;
    outline: none;
    transition: background-color 0.2s ease;
  }
  input[type="range"]:hover {
    background: #a084ca;
  }
  input[type="range"]::-webkit-slider-thumb {
    -webkit-appearance: none;
    appearance: none;
    width: 22px;
    height: 22px;
    background: #f0e130;
    cursor: pointer;
    border-radius: 50%;
    border: 2px solid #333;
    transition: background-color 0.3s ease;
  }
  input[type="range"]::-webkit-slider-thumb:hover {
    background: #ddd820;
  }
  input[type="range"]::-moz-range-thumb {
    width: 22px;
    height: 22px;
    background: #f0e130;
    cursor: pointer;
    border-radius: 50%;
    border: 2px solid #333;
  }
  input[type="number"] {
    width: 60px;
    background: #3e3e6e;
    border: none;
    border-radius: 8px;
    color: #eee;
    font-weight: 600;
    font-size: 0.9rem;
    text-align: center;
    padding: 4px 8px;
    margin-left: 15px;
    box-shadow: inset 0 0 6px #6666cc;
    transition: box-shadow 0.3s ease;
    user-select: text;
  }
  input[type="number"]:focus {
    outline: none;
    box-shadow: 0 0 12px #f0e130;
    color: #fff;
  }
</style>
</head>
<body>

<h1>Batch Upscaler</h1>

<div class="mode-toggle">
  <button id="btnPS99" class="mode-btn active">PS99</button>
  <button id="btnPSX" class="mode-btn">PSX</button>
</div>

<div class="container">
  <input type="file" id="uploadBatch" accept="image/*" multiple />

  <div class="controls" id="controlsContainer">
    <div class="control-group">
      <label for="sliderX">X Offset:</label>
      <input type="range" id="sliderX" min="-250" max="0" step="0.1" value="-88" />
      <input type="number" id="inputX" min="-250" max="0" step="0.1" value="-88" />
    </div>
    <div class="control-group">
      <label for="sliderY">Y Offset:</label>
      <input type="range" id="sliderY" min="-250" max="0" step="0.1" value="-91" />
      <input type="number" id="inputY" min="-250" max="0" step="0.1" value="-91" />
    </div>
    <div class="control-group">
      <label for="sliderSize">Size (width & height):</label>
      <input type="range" id="sliderSize" min="250" max="600" step="0.1" value="353" />
      <input type="number" id="inputSize" min="250" max="600" step="0.1" value="353" />
    </div>
  </div>

  <div id="previewContainer"></div>

  <div class="buttons">
    <button id="upscaleAllBtn" disabled>Upscale & Apply Border All</button>
    <button id="downloadIndividualBtn" disabled>Download Individually</button>
    <button id="downloadZipBtn" disabled>Download as ZIP</button>
  </div>
  <div id="version">Version 11</div>
</div>

<div id="footer">Made by @Versefy</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>
<script>
  const uploadBatch = document.getElementById('uploadBatch');
  const previewContainer = document.getElementById('previewContainer');
  const upscaleAllBtn = document.getElementById('upscaleAllBtn');
  const downloadIndividualBtn = document.getElementById('downloadIndividualBtn');
  const downloadZipBtn = document.getElementById('downloadZipBtn');
  const btnPS99 = document.getElementById('btnPS99');
  const btnPSX = document.getElementById('btnPSX');

  const sliderX = document.getElementById('sliderX');
  const sliderY = document.getElementById('sliderY');
  const sliderSize = document.getElementById('sliderSize');

  const inputX = document.getElementById('inputX');
  const inputY = document.getElementById('inputY');
  const inputSize = document.getElementById('inputSize');

  let batchImages = [];
  let currentMode = 'PS99';

  const defaultSettings = {
    PS99: { x: -88, y: -91, size: 353, borderWidth: 8 },
    PSX: { x: -150, y: -98.8, size: 411.5, borderWidth: 9 }
  };

  function clearPreviews() {
    previewContainer.innerHTML = '';
    batchImages = [];
    upscaleAllBtn.disabled = true;
    downloadIndividualBtn.disabled = true;
    downloadZipBtn.disabled = true;
  }

  uploadBatch.addEventListener('change', e => {
    const files = e.target.files;
    clearPreviews();
    if (files.length === 0) return;
    Array.from(files).forEach(file => {
      const img = new Image();
      const reader = new FileReader();
      reader.onload = ev => {
        img.onload = () => {
          const canvas = document.createElement('canvas');
          canvas.width = 250;
          canvas.height = 250;
          const ctx = canvas.getContext('2d');
          ctx.clearRect(0, 0, 420, 420);
          ctx.drawImage(img, 0, 0, 420, 420);
          previewContainer.appendChild(canvas);
          batchImages.push({ img, canvas, isUpscaled: false, filename: file.name });
          upscaleAllBtn.disabled = false;
          downloadIndividualBtn.disabled = false;
          downloadZipBtn.disabled = false;
        };
        img.src = ev.target.result;
      };
      reader.readAsDataURL(file);
    });
  });

  // Sync slider and number input
  function syncSliderAndInput(slider, input) {
    slider.addEventListener('input', () => {
      input.value = slider.value;
      onControlChange();
    });
    input.addEventListener('input', () => {
      let val = parseFloat(input.value);
      if (isNaN(val)) return;
      // Clamp values to slider min/max
      val = Math.min(Math.max(val, parseFloat(slider.min)), parseFloat(slider.max));
      input.value = val;
      slider.value = val;
      onControlChange();
    });
  }
  syncSliderAndInput(sliderX, inputX);
  syncSliderAndInput(sliderY, inputY);
  syncSliderAndInput(sliderSize, inputSize);

  function onControlChange() {
    batchImages.forEach(item => {
      if (item.isUpscaled) {
        upscaleImage(item);
      }
    });
  }

  // Draw upscale + border on a canvas item
  function upscaleImage(item) {
    const { img, canvas } = item;
    const ctx = canvas.getContext('2d');
    ctx.clearRect(0, 0, 250, 250);

    const x = parseFloat(sliderX.value);
    const y = parseFloat(sliderY.value);
    const size = parseFloat(sliderSize.value);
    const borderWidth = currentMode === 'PS99' ? 8 : 9;

    ctx.drawImage(img, x, y, size, size);
    ctx.lineWidth = borderWidth;
    ctx.strokeStyle = 'black';
    ctx.strokeRect(borderWidth / 2, borderWidth / 2, 420 - borderWidth, 420 - borderWidth);
  }

  function upscaleAll() {
    batchImages.forEach(item => {
      upscaleImage(item);
      item.isUpscaled = true;
    });
  }

  // Download helper
  function triggerDownload(dataUrl, filename) {
    const link = document.createElement('a');
    link.href = dataUrl;
    link.download = filename;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  }

  async function downloadIndividual() {
    for (const { img, isUpscaled, filename } of batchImages) {
      const exportCanvas = document.createElement('canvas');
      exportCanvas.width = 250;
      exportCanvas.height = 250;
      const exportCtx = exportCanvas.getContext('2d');

      if (isUpscaled) {
        const x = parseFloat(sliderX.value);
        const y = parseFloat(sliderY.value);
        const size = parseFloat(sliderSize.value);
        const borderWidth = currentMode === 'PS99' ? 8 : 9;

        exportCtx.drawImage(img, x, y, size, size);
        exportCtx.lineWidth = borderWidth;
        exportCtx.strokeStyle = 'black';
        exportCtx.strokeRect(borderWidth / 2, borderWidth / 2, 420 - borderWidth, 420 - borderWidth);
      } else {
        exportCtx.drawImage(img, 0, 0, 420, 420);
      }

      const dataUrl = exportCanvas.toDataURL('image/png');
      let saveName = filename;
      if (saveName.includes('.')) {
        const parts = saveName.split('.');
        parts[parts.length - 2] += '-upscaled';
        saveName = parts.join('.');
      } else {
        saveName += '-upscaled.png';
      }
      triggerDownload(dataUrl, saveName);
      // Small delay so downloads don't clash
      await new Promise((r) => setTimeout(r, 400));
    }
  }

  async function downloadZip() {
    if (batchImages.length === 0) return;
    const zip = new JSZip();

    batchImages.forEach(({ img, isUpscaled, filename }) => {
      const exportCanvas = document.createElement('canvas');
      exportCanvas.width = 250;
      exportCanvas.height = 250;
      const exportCtx = exportCanvas.getContext('2d');

      if (isUpscaled) {
        const x = parseFloat(sliderX.value);
        const y = parseFloat(sliderY.value);
        const size = parseFloat(sliderSize.value);
        const borderWidth = currentMode === 'PS99' ? 8 : 9;

        exportCtx.drawImage(img, x, y, size, size);
        exportCtx.lineWidth = borderWidth;
        exportCtx.strokeStyle = 'black';
        exportCtx.strokeRect(borderWidth / 2, borderWidth / 2, 250 - borderWidth, 250 - borderWidth);
      } else {
        exportCtx.drawImage(img, 0, 0, 420, 420);
      }
      const dataUrl = exportCanvas.toDataURL('image/png');
      const base64 = dataUrl.split(',')[1];
      let fileName = filename;
      if (fileName.includes('.')) {
        const parts = fileName.split('.');
        parts[parts.length - 2] += '-upscaled';
        fileName = parts.join('.');
      } else {
        fileName += '-upscaled.png';
      }
      zip.file(fileName, base64, { base64: true });
    });

    const content = await zip.generateAsync({ type: 'blob' });
    const zipLink = document.createElement('a');
    zipLink.href = URL.createObjectURL(content);
    zipLink.download = 'upscaled_images.zip';
    document.body.appendChild(zipLink);
    zipLink.click();
    document.body.removeChild(zipLink);
  }

  // Mode switching updates slider defaults
  function setActiveMode(mode) {
    currentMode = mode;
    btnPS99.classList.toggle('active', mode === 'PS99');
    btnPSX.classList.toggle('active', mode === 'PSX');

    if (mode === 'PS99') {
      sliderX.min = -250; sliderX.max = 0; sliderX.step = 0.1;
      sliderY.min = -250; sliderY.max = 0; sliderY.step = 0.1;
      sliderSize.min = 250; sliderSize.max = 600; sliderSize.step = 0.1;

      sliderX.value = defaultSettings.PS99.x;
      sliderY.value = defaultSettings.PS99.y;
      sliderSize.value = defaultSettings.PS99.size;

      inputX.value = defaultSettings.PS99.x;
      inputY.value = defaultSettings.PS99.y;
      inputSize.value = defaultSettings.PS99.size;
    } else {
      sliderX.min = -250; sliderX.max = 0; sliderX.step = 0.1;
      sliderY.min = -250; sliderY.max = 0; sliderY.step = 0.1;
      sliderSize.min = 250; sliderSize.max = 600; sliderSize.step = 0.1;

      sliderX.value = defaultSettings.PSX.x;
      sliderY.value = defaultSettings.PSX.y;
      sliderSize.value = defaultSettings.PSX.size;

      inputX.value = defaultSettings.PSX.x;
      inputY.value = defaultSettings.PSX.y;
      inputSize.value = defaultSettings.PSX.size;
    }

    // Re-upscale all images with new settings if upscaled already
    batchImages.forEach(item => {
      if (item.isUpscaled) {
        upscaleImage(item);
      }
    });
  }

  // Event listeners for buttons
  btnPS99.addEventListener('click', () => setActiveMode('PS99'));
  btnPSX.addEventListener('click', () => setActiveMode('PSX'));
  upscaleAllBtn.addEventListener('click', upscaleAll);
  downloadIndividualBtn.addEventListener('click', downloadIndividual);
  downloadZipBtn.addEventListener('click', downloadZip);

  // Initialize mode & controls
  setActiveMode('PS99');
</script>

</body>
</html>
