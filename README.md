<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Batch Upscaler</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap');

  * {
    box-sizing: border-box;
  }
  body {
    font-family: 'Poppins', Arial, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    margin: 0; padding: 40px 0;
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
    max-width: 380px;
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
</style>
</head>
<body>

<h1>Batch Upscaler</h1>

<div class="container">
  <input type="file" id="uploadBatch" accept="image/*" multiple />
  <div id="previewContainer"></div>
  <div class="buttons">
    <button id="upscaleAllBtn" disabled>Upscale & Apply Border All</button>
    <button id="downloadAllBtn" disabled>Download All Individually</button>
  </div>
  <div id="version">Version 4</div>
</div>

<script>
  const uploadBatch = document.getElementById('uploadBatch');
  const previewContainer = document.getElementById('previewContainer');
  const upscaleAllBtn = document.getElementById('upscaleAllBtn');
  const downloadAllBtn = document.getElementById('downloadAllBtn');

  let batchImages = []; // {img: Image, canvas, isUpscaled}

  uploadBatch.addEventListener('change', e => {
    const files = e.target.files;
    batchImages = [];
    previewContainer.innerHTML = '';
    if (files.length === 0) {
      upscaleAllBtn.disabled = true;
      downloadAllBtn.disabled = true;
      return;
    }
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
          batchImages.push({ img, canvas, isUpscaled: false });
          upscaleAllBtn.disabled = false;
          downloadAllBtn.disabled = false;
        };
        img.src = ev.target.result;
      };
      reader.readAsDataURL(file);
    });
  });

  upscaleAllBtn.addEventListener('click', () => {
    batchImages.forEach(({img, canvas}) => {
      const ctx = canvas.getContext('2d');
      ctx.clearRect(0, 0, 250, 250);
      // Upscale 353x353 drawn at offset -88,-91
      ctx.drawImage(img, -88, -91, 353, 353);
      // Draw black border inside 250x250 box
      ctx.lineWidth = 8;
      ctx.strokeStyle = 'black';
      ctx.strokeRect(4, 4, 242, 242);
    });
    batchImages.forEach(item => item.isUpscaled = true);
  });

  downloadAllBtn.addEventListener('click', () => {
    batchImages.forEach(({img, isUpscaled}) => {
      const exportCanvas = document.createElement('canvas');
      exportCanvas.width = 250;
      exportCanvas.height = 250;
      const exportCtx = exportCanvas.getContext('2d');
      if(isUpscaled){
        exportCtx.drawImage(img, -88, -91, 353, 353);
        exportCtx.lineWidth = 8;
        exportCtx.strokeStyle = 'black';
        exportCtx.strokeRect(4, 4, 242, 242);
      } else {
        exportCtx.drawImage(img, 0, 0, 250, 250);
      }
      const link = document.createElement('a');
      link.download = 'upscaled_image.png';
      link.href = exportCanvas.toDataURL('image/png');
      link.click();
    });
  });
</script>

</body>
</html>
