<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pet Simulator X Icon Converter</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

    @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@800&display=swap');

    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    body {
        font-family: 'Poppins', sans-serif;
        font-weight: 800;
        color: white;
        text-shadow: 1px 1px 0px #000000, -1px -1px 0px #000000, 1px -1px 0px #000000, -1px 1px 0px #000000;
        background: linear-gradient(135deg, #ffffff 0%, #ffffff 100%);
        min-height: 100vh;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        padding: 20px;
        position: relative;
    }

        body::before {
            content: '';
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background-image: url('https://tr.rbxcdn.com/180DAY-63613e0c81a7804328b0f97d13522c1f/420/420/Image/Webp/noFilter');
            background-size: 171px 135px;
            background-position: 0 0;
            background-repeat: repeat;
            filter: brightness(0) saturate(100%) invert(89%) sepia(3%) saturate(191%) hue-rotate(202deg) brightness(95%) contrast(88%);
            opacity: 0.1;
            z-index: -1;
        }

        .container {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 40px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
            max-width: 900px;
            width: 100%;
        }

        h1 {
            text-align: center;
            color: white;
             text-shadow: 3px 3px 3px #000000, -1px -1px 0px #000000, 1px -1px 1px #000000, -1px 1px 0px #000000;
            margin-bottom: 30px;
            font-size: 3em;
            font-weight: 800;
        }

        .upload-area {
            border: 3px dashed #667eea;
            border-radius: 15px;
            padding: 40px;
            text-align: center;
            margin-bottom: 30px;
            transition: all 0.3s ease;
            cursor: pointer;
        }

        .upload-area:hover {
            border-color: #764ba2;
            background: rgba(102, 126, 234, 0.05);
        }

        .upload-area.dragover {
            border-color: #764ba2;
            background: rgba(102, 126, 234, 0.1);
            transform: scale(1.02);
        }

        .upload-text {
            color: white;
            text-shadow: 3px 3px 3px #000000, -1px -1px 0px #000000, 1px -1px 1px #000000, -1px 1px 0px #000000;
            font-size: 1.2em;
            font-weight: 800;
            margin-bottom: 10px;
        }

        .file-input {
            display: none;
        }

        .color-selector {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-bottom: 30px;
        }

        .color-option {
            width: 60px;
            height: 60px;
            border-radius: 50%;
            border: 4px solid transparent;
            cursor: pointer;
            transition: all 0.3s ease;
            position: relative;
        }

        .color-option:hover {
            transform: scale(1.1);
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }

        .color-option.selected {
            border-color: #333;
            transform: scale(1.15);
        }
        
        .color-option.hue.selected {
            border-color: transparent;
        }

        .color-option.black { background: #000; }
        .color-option.white { background: #fff; border: 2px solid #ddd; }
        .color-option.hue { 
            background: radial-gradient(circle, #000000 0%, #bf68b9 57%, #ed81e6 100%);
        }

        .shift-control {
            display: none;
        }

        .result-container {
            position: relative;
            text-align: center;
            margin-top: 30px;
            display: none;
        }

        .canvas-container {
            position: relative;
            display: inline-block;
            border-radius: 15px;
            overflow: hidden;
            box-shadow: 0 15px 30px rgba(0, 0, 0, 0.2);
        }

        canvas {
            display: block;
            max-width: 100%;
            height: auto;
        }

        .processing {
            color: white;
            text-shadow: 2px 2px 0px #000000, -2px -2px 0px #000000, 2px -2px 0px #000000, -2px 2px 0px #000000;
            font-size: 1.1em;
            font-weight: 800;
            margin-top: 20px;
        }

        .download-btn {
            background: linear-gradient(135deg, #ffffff 0%, #ffffff 100%);
            color: white;
            text-shadow: 2px 2px 0px #000000, -2px -2px 0px #000000, 2px -2px 0px #000000, -2px 2px 0px #000000;
            border: none;
            padding: 12px 30px;
            border-radius: 25px;
            font-size: 1.1em;
            font-weight: 800;
            cursor: pointer;
            margin-top: 20px;
            transition: all 0.3s ease;
            box-shadow: 0 5px 15px rgba(0,0,0, 0.3);
        }

        .download-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(0,0,0, 0.4);
        }

        .download-btn:active {
            transform: translateY(0);
        }

        .download-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: none;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>PET SIMULATOR X ICON CONVERTER</h1>
        
        <div class="upload-area" id="uploadArea">
            <div class="upload-text">Drop an image here or click to select</div>
            <input type="file" id="fileInput" class="file-input" accept="image/*">
        </div>

        <div class="color-selector">
            <div class="color-option black selected" data-color="black" title="Black Overlay"></div>
            <div class="color-option white" data-color="white" title="White Overlay"></div>
            <div class="color-option hue" data-color="hue" title="Pink Hue"></div>
        </div>

        <div class="result-container" id="resultContainer">
            <div class="canvas-container">
                <canvas id="outputCanvas"></canvas>
            </div>
            <div class="processing" id="processingText">Processing your image...</div>
            <button class="download-btn" id="downloadBtn" onclick="downloadImage()" style="display: none;">
                Download Image
            </button>
        </div>
    </div>

    <script>
        const uploadArea = document.getElementById('uploadArea');
        const fileInput = document.getElementById('fileInput');
        const resultContainer = document.getElementById('resultContainer');
        const outputCanvas = document.getElementById('outputCanvas');
        const processingText = document.getElementById('processingText');
        const colorOptions = document.querySelectorAll('.color-option');
        const downloadBtn = document.getElementById('downloadBtn');
        
        let selectedColor = 'black';
        let currentImage = null;
        let shiftAmount = 5; // Fixed at 5px

        // Color selection
        colorOptions.forEach(option => {
            option.addEventListener('click', () => {
                colorOptions.forEach(opt => opt.classList.remove('selected'));
                option.classList.add('selected');
                selectedColor = option.dataset.color;
                
                if (currentImage) {
                    processImage(currentImage);
                }
            });
        });

        // Upload area interactions
        uploadArea.addEventListener('click', () => fileInput.click());
        
        uploadArea.addEventListener('dragover', (e) => {
            e.preventDefault();
            uploadArea.classList.add('dragover');
        });
        
        uploadArea.addEventListener('dragleave', () => {
            uploadArea.classList.remove('dragover');
        });
        
        uploadArea.addEventListener('drop', (e) => {
            e.preventDefault();
            uploadArea.classList.remove('dragover');
            const files = e.dataTransfer.files;
            if (files.length > 0) {
                handleFile(files[0]);
            }
        });

        fileInput.addEventListener('change', (e) => {
            if (e.target.files.length > 0) {
                handleFile(e.target.files[0]);
            }
        });

        function handleFile(file) {
            if (!file.type.startsWith('image/')) {
                alert('Please select a valid image file.');
                return;
            }

            const reader = new FileReader();
            reader.onload = (e) => {
                const img = new Image();
                img.onload = () => {
                    currentImage = img;
                    processImage(img);
                };
                img.src = e.target.result;
            };
            reader.readAsDataURL(file);
        }

        function createGradientFill(ctx, width, height) {
            const centerX = width / 2;
            const centerY = height / 2;
            const radius = Math.max(width, height);
            
            const gradient = ctx.createRadialGradient(centerX, centerY, 0, centerX, centerY, radius);
            gradient.addColorStop(0, '#000000');
            gradient.addColorStop(0.57, '#bf68b9');
            gradient.addColorStop(1, '#ed81e6');
            
            return gradient;
        }

        function applyHueTransform(canvas, ctx, img) {
            // Create a temporary canvas for the hue transformation
            const tempCanvas = document.createElement('canvas');
            const tempCtx = tempCanvas.getContext('2d');
            tempCanvas.width = img.width;
            tempCanvas.height = img.height;
            
            // Draw the original image to temp canvas
            tempCtx.drawImage(img, 0, 0);
            
            // Apply the CSS filter transformation to match our golden-to-pink conversion
            tempCtx.filter = 'hue-rotate(-120deg) saturate(1.131) brightness(1)';
            
            // Draw the filtered image onto itself
            tempCtx.globalCompositeOperation = 'copy';
            tempCtx.drawImage(tempCanvas, 0, 0);
            
            return tempCanvas;
        }

        function processImage(img) {
            resultContainer.style.display = 'block';
            processingText.style.display = 'block';
            
            setTimeout(() => {
                const canvas = outputCanvas;
                const ctx = canvas.getContext('2d');
                
                const outlineWidth = 11;
                canvas.width = img.width + shiftAmount + outlineWidth * 2;
                canvas.height = img.height + outlineWidth * 2;
                
                const basePositionX = outlineWidth + shiftAmount;
                const basePositionY = outlineWidth;
                
                ctx.clearRect(0, 0, canvas.width, canvas.height);

                let fillStyle;
                if (selectedColor === 'hue') {
                    // Use the gradient from the first script
                    fillStyle = createGradientFill(ctx, canvas.width, canvas.height);
                } else {
                    fillStyle = selectedColor === 'black' ? '#000000' : '#ffffff';
                }

                // Create outline
                ctx.save();
                for (let x = -outlineWidth; x <= outlineWidth; x++) {
                    for (let y = -outlineWidth; y <= outlineWidth; y++) {
                        if (x * x + y * y <= outlineWidth * outlineWidth) {
                            ctx.drawImage(img, basePositionX - shiftAmount + x, basePositionY + y);
                        }
                    }
                }
                ctx.globalCompositeOperation = 'source-in';
                ctx.fillStyle = fillStyle;
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                ctx.restore();

                // Prepare the top layer image (keep the pink hue transformation)
                let topLayerImage = img;
                if (selectedColor === 'hue') {
                    topLayerImage = applyHueTransform(canvas, ctx, img);
                }
                
                // Draw the main image
                ctx.drawImage(topLayerImage, basePositionX, basePositionY);
                
                processingText.style.display = 'none';
                downloadBtn.style.display = 'inline-block';
            }, 100);
        }

        function downloadImage() {
            const canvas = outputCanvas;
            const link = document.createElement('a');
            link.download = 'processed-icon.png';
            link.href = canvas.toDataURL();
            link.click();
        }
    </script>
</body>
</html>
