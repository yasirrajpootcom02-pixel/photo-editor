<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Simple Photo Editor</title>
  <style>
    body {
      font-family: sans-serif;
      text-align: center;
      background: #f0f0f0;
    }
    .editor {
      margin-top: 20px;
    }
    canvas {
      border: 1px solid #333;
      max-width: 100%;
    }
    .controls {
      margin-top: 15px;
    }
  </style>
</head>
<body>
  <h1>Simple Photo Editor</h1>
  <input type="file" id="upload" accept="image/*"><br/><br/>
  <div class="editor">
    <canvas id="canvas"></canvas>
    <div class="controls">
      <button onclick="rotate()">Rotate</button>
      <button onclick="brightness(10)">Brightness +</button>
      <button onclick="brightness(-10)">Brightness -</button>
      <button onclick="download()">Download</button>
    </div>
  </div>

  <script>
    let canvas = document.getElementById('canvas');
    let ctx = canvas.getContext('2d');
    let img = new Image();
    let angle = 0;

    document.getElementById('upload').onchange = function (e) {
      let reader = new FileReader();
      reader.onload = function (event) {
        img.onload = () => {
          canvas.width = img.width;
          canvas.height = img.height;
          ctx.drawImage(img, 0, 0);
        };
        img.src = event.target.result;
      };
      reader.readAsDataURL(e.target.files[0]);
    };

    function rotate() {
      angle += 90;
      let tempCanvas = document.createElement('canvas');
      let tempCtx = tempCanvas.getContext('2d');
      tempCanvas.width = canvas.height;
      tempCanvas.height = canvas.width;
      tempCtx.translate(tempCanvas.width / 2, tempCanvas.height / 2);
      tempCtx.rotate((angle * Math.PI) / 180);
      tempCtx.drawImage(canvas, -canvas.width / 2, -canvas.height / 2);
      canvas.width = tempCanvas.width;
      canvas.height = tempCanvas.height;
      ctx.drawImage(tempCanvas, 0, 0);
    }

    function brightness(value) {
      let imgData = ctx.getImageData(0, 0, canvas.width, canvas.height);
      for (let i = 0; i < imgData.data.length; i += 4) {
        imgData.data[i] += value;
        imgData.data[i + 1] += value;
        imgData.data[i + 2] += value;
      }
      ctx.putImageData(imgData, 0, 0);
    }

    function download() {
      let link = document.createElement('a');
      link.download = 'edited-image.png';
      link.href = canvas.toDataURL();
      link.click();
    }
  </script>
</body>
</html>
