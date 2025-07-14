<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>카메라 분류 앱</title>
    <style>
      body {
        font-family: sans-serif;
        text-align: center;
        background-color: #111;
        color: white;
      }
      video {
        border: 1px solid white;
      }
      #label-container {
        font-size: 1.2em;
        font-weight: bold;
        margin-top: 10px;
      }
      button {
        padding: 10px 20px;
        font-size: 16px;
        margin-top: 20px;
      }
    </style>
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs"></script>
    <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@0.8/dist/teachablemachine-image.min.js"></script>
  </head>
  <body>
    <h1>카메라 분류 앱</h1>
    <button onclick="init()">카메라 시작</button>
    <div id="webcam-container"></div>
    <div id="label-container">결과 없음</div>

    <script type="text/javascript">
      const URL = "https://teachablemachine.withgoogle.com/models/O4Kie2-u-/";
      let model, webcam, labelContainer, maxPredictions;

      async function init() {
        const modelURL = URL + "model.json";
        const metadataURL = URL + "metadata.json";

        model = await tmImage.load(modelURL, metadataURL);
        maxPredictions = model.getTotalClasses();

        const flip = true;
        webcam = new tmImage.Webcam(200, 200, flip);
        await webcam.setup(); // 카메라 요청
        await webcam.play();
        window.requestAnimationFrame(loop);

        document.getElementById("webcam-container").appendChild(webcam.canvas);
        labelContainer = document.getElementById("label-container");

        for (let i = 0; i < maxPredictions; i++) {
          labelContainer.appendChild(document.createElement("div"));
        }
      }

      async function loop() {
        webcam.update();
        await predict();
        window.requestAnimationFrame(loop);
      }

      async function predict() {
        const prediction = await model.predict(webcam.canvas);
        for (let i = 0; i < maxPredictions; i++) {
          const classPrediction =
            prediction[i].className + ": " + (prediction[i].probability * 100).toFixed(2) + "%";
          labelContainer.childNodes[i].innerHTML = classPrediction;
        }
      }
    </script>
  </body>
</html>
