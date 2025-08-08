<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Batch Upscaler PS99 / PSX</title>
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
    margin-bottom: 30px;
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
    width: 420px;
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
    max-width: 380px;
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
  }
  button, select {
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
    max-width: 380px;
  }
  button:hover:not(:disabled), select:hover {
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
  <div id="previewContainer"></div>

  <div class="buttons">
    <button id="upscaleAllBtn" disabled>Upscale & Apply Border All</button>
    <button id="downloadIndividualBtn" disabled>Download Individually</button>
    <button id="downloadZipBtn" disabled>Download as ZIP</button>
  </div>
  <div id="version">Version 7</div>
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

  let batchImages = []; // {img: Image, canvas, isUpscaled, filename}
  let currentMode = 'PS99'; // default mode

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
          ctx.clearRect(0,0,250,250);
          ctx.drawImage(img, 0, 0, 250, 250);
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

  function upscaleImage(item) {
    const {img, canvas} = item;
    const ctx = canvas.getContext('2d');
    ctx.clearRect(0, 0, 250, 250);

    if(currentMode === 'PS99') {
      // PS99: X=-88, Y=-91, size 353x353, border 8px
      ctx.drawImage(img, -88, -91, 353, 353);
      ctx.lineWidth = 8;
    } else {
      // PSX: X=-53, Y=-96, size 411.5x411.5, border 9px
      ctx.drawImage(img, -53, -96, 411.5, 411.5);
      ctx.lineWidth = 9;
    }

    ctx.strokeStyle = 'black';
    ctx.strokeRect(4, 4, 242, 242);
  }

  upscaleAllBtn.addEventListener('click', () => {
    batchImages.forEach(item => {
      upscaleImage(item);
      item.isUpscaled = true;
    });
  });

  async function downloadIndividual() {
    for (const {img, isUpscaled, filename} of batchImages) {
      const exportCanvas = document.createElement('canvas');
      exportCanvas.width = 250;
      exportCanvas.height = 250;
      const exportCtx = exportCanvas.getContext('2d');

      if(isUpscaled){
        if(currentMode === 'PS99') {
          exportCtx.drawImage(img, -88, -91, 353, 353);
          exportCtx.lineWidth = 8;
        } else {
          exportCtx.drawImage(img, -53, -96, 411.5, 411.5);
          exportCtx.lineWidth = 9;
        }
        exportCtx.strokeStyle = 'black';
        exportCtx.strokeRect(4, 4, 242, 242);
      } else {
        exportCtx.drawImage(img, 0, 0, 250, 250);
      }

      const dataUrl = exportCanvas.toDataURL('image/png');
      const link = document.createElement('a');
      link.download = filename.includes('.') ? filename.replace(/\.(?=[^.]+$)/, '-upscaled.') : filename + '-upscaled.png';
      link.href = dataUrl;
      link.click();
      await new Promise(r => setTimeout(r, 250)); // slight delay so browser can process downloads
    }
  }

  async function downloadZip() {
    if(batchImages.length === 0) return;
    const zip = new JSZip();

    batchImages.forEach(({img, isUpscaled, filename}) => {
      const exportCanvas = document.createElement('canvas');
      exportCanvas.width = 250;
      exportCanvas.height = 250;
      const exportCtx = exportCanvas.getContext('2d');

      if(isUpscaled){
        if(currentMode === 'PS99') {
          exportCtx.drawImage(img, -88, -91, 353, 353);
          exportCtx.lineWidth = 8;
        } else {
          exportCtx.drawImage(img, -53, -96, 411.5, 411.5);
          exportCtx.lineWidth = 9;
        }
        exportCtx.strokeStyle = 'black';
        exportCtx.strokeRect(4, 4, 242, 242);
      } else {
        exportCtx.drawImage(img, 0, 0, 250, 250);
      }
      const dataUrl = exportCanvas.toDataURL('image/png');
      const base64 = dataUrl.split(',')[1];
      let fileName = filename;
      if(fileName.includes('.')){
        const parts = fileName.split('.');
        parts[parts.length-2] += '-upscaled';
        fileName = parts.join('.');
      } else {
        fileName += '-upscaled.png';
      }
      zip.file(fileName, base64, {base64:true});
    });

    const content = await zip.generateAsync({type:"blob"});
    const link = document.createElement('a');
    link.href = URL.createObjectURL(content);
    link.download = "upscaled_images.zip";
    link.click();
    URL.revokeObjectURL(link.href);
  }

  function setActiveMode(mode) {
    currentMode = mode;
    if(mode === 'PS99') {
      btnPS99.classList.add('active');
      btnPSX.classList.remove('active');
    } else {
      btnPS99.classList.remove('active');
      btnPSX.classList.add('active');
    }
    // Reset upscale state and redraw previews unscaled:
    batchImages.forEach(({img, canvas}) => {
      const ctx = canvas.getContext('2d');
      ctx.clearRect(0, 0, 250, 250);
      ctx.drawImage(img, 0, 0, 250, 250);
    });
    batchImages.forEach(item => item.isUpscaled = false);
    upscaleAllBtn.disabled = batchImages.length === 0;
    downloadIndividualBtn.disabled = batchImages.length === 0;
    downloadZipBtn.disabled = batchImages.length === 0;
  }

  btnPS99.addEventListener('click', () => setActiveMode('PS99'));
  btnPSX.addEventListener('click', () => setActiveMode('PSX'));
</script>

</body>
</html>
