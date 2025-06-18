<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>3D Color Dice Roller</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: url('Background.jpeg') no-repeat center center fixed;
      background-size: cover;
      text-align: center;
      padding: 20px;
      background-size: 50%;
      background-position: top;
    }

 @font-face {
  font-family: 'Marvin';
  src: url('/font/Marvin.ttf') format('truetype');
}


    select {
      font-size: 18px;
      margin: 10px;
      padding: 6px 12px;
      border-radius: 6px;
      border: 2px solid #0586F9;
    }

select,
.roll-button {
  font-family: 'Marvin', sans-serif;
}
select{
  color: #0586F9;
}
.roll-button{
  color: #FDFFFC;
  text-shadow: 1px 1px 1px rgba(0, 0, 0, 0.4);
  -webkit-text-stroke: 0.1px #C9941E;
}

    .dice-area {
      display: flex;
      justify-content: center;
      gap: 40px;
      margin: 40px 0;
      flex-wrap: wrap;
    }

    .dice-wrapper {
      text-align: center;
    }

    .scene {
      perspective: 800px;
    }

    .dice {
      width: 70px;
      height: 70px;
      position: relative;
      transform-style: preserve-3d;
      transform: rotateX(0deg) rotateY(0deg);
      transition: transform 1.4s ease-in-out;
      margin: 0 auto;
    }

    .face {
      position: absolute;
      width: 70px;
      height: 70px;
      display: flex;
      justify-content: center;
      align-items: center;
      color: white;
      font-size: 30px;
      font-weight: bold;
      border-radius: 10px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.3);
      border: 3px solid black;
    }

    .front  { transform: translateZ(35px); }
    .back   { transform: rotateY(180deg) translateZ(35px); }
    .left   { transform: rotateY(-90deg) translateZ(35px); }
    .right  { transform: rotateY(90deg) translateZ(35px); }
    .top    { transform: rotateX(90deg) translateZ(35px); }
    .bottom { transform: rotateX(-90deg) translateZ(35px); }

    .roll-button {
      font-size: 20px;
      padding: 15px 40px;
      background: yellow;
      border: none;
      border-radius: 10px;
      font-weight: bold;
      cursor: pointer;
      margin-bottom: 20px;
    }

    .colors-list {
      margin-top: 10px;
      font-size: 16px;
    }

    .color-sample {
      font-weight: bold;
    }

    .color-red { color: red; }
    .color-orange { color: orange; }
    .color-yellow { color: yellow; }
    .color-green { color: green; }
    .color-blue { color: blue; }
    .color-purple { color: purple; }

    .result-text {
      font-size: 18px;
      margin-top: 10px;
      font-weight: bold;
      height: 24px;
    }

    .exclude-colors {
      margin: 20px auto;
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      gap: 10px;
      max-width: 500px;
    }

    .exclude-colors label {
      font-size: 14px;
    }
  </style>
</head>
<body>

  <!-- Sound effect -->
  <audio id="diceSound" src="SoundEffect.mp4" preload="auto"></audio>

  <!-- Dice Type Selectors (Non-functional placeholders) -->
  <select>
    <option>3 DICE</option>
  </select>
  <select>
    <option>COLOR DICE</option>
  </select>

  <div class="scene">
    <div class="dice-area" id="diceArea"></div>
  </div>

  <button class="roll-button" onclick="rollDice()">ROLL AGAIN !</button>

  <div class="colors-list">
    Possible colors are:
    <span class="color-sample color-red">Red</span>,
    <span class="color-sample color-orange">Orange</span>,
    <span class="color-sample color-green">Green</span>,
    <span class="color-sample color-yellow">Yellow</span>,
    <span class="color-sample color-blue">Blue</span>, and
    <span class="color-sample color-purple">Purple</span>.
  </div>

  <div class="exclude-colors">
    <label><input type="checkbox" value="red" /> Red</label>
    <label><input type="checkbox" value="orange" /> Orange</label>
    <label><input type="checkbox" value="yellow" /> Green</label>
    <label><input type="checkbox" value="green" /> Yellow</label>
    <label><input type="checkbox" value="blue" /> Blue</label>
    <label><input type="checkbox" value="purple" /> Purple</label>
  </div>

  <script>
    const faceConfigs = [
  { face: "front", color: "red", x: 0, y: 0 },
  { face: "back", color: "orange", x: 0, y: 180 },
  { face: "left", color: "green", x: 0, y: -90 }, // Changed to green
  { face: "right", color: "yellow", x: 0, y: 90 }, // Changed to yellow
  { face: "top", color: "blue", x: -90, y: 0 },
  { face: "bottom", color: "purple", x: 90, y: 0 }
];

    function createDice() {
      const wrapper = document.createElement("div");
      wrapper.className = "dice-wrapper";

      const dice = document.createElement("div");
      dice.className = "dice";

      faceConfigs.forEach(config => {
        const div = document.createElement("div");
        div.className = `face ${config.face}`;
        div.style.backgroundColor = config.color;
        div.innerHTML = "•";
        dice.appendChild(div);
      });

      const resultText = document.createElement("div");
      resultText.className = "result-text";

      wrapper.appendChild(dice);
      wrapper.appendChild(resultText);

      return { wrapper, dice };
    }

    function getIncludedConfigs() {
      const checkboxes = document.querySelectorAll('.exclude-colors input[type="checkbox"]');
      const excludedColors = Array.from(checkboxes)
                                  .filter(cb => cb.checked)
                                  .map(cb => cb.value.toLowerCase());
      return faceConfigs.filter(config => !excludedColors.includes(config.color.toLowerCase()));
    }

    function rollDice() {
      const sound = document.getElementById("diceSound");
      sound.currentTime = 0;
      sound.play();

      const diceArea = document.getElementById("diceArea");
      diceArea.innerHTML = "";

      const includedConfigs = getIncludedConfigs();

      if (includedConfigs.length === 0) {
        diceArea.innerHTML = "<p style='font-weight:bold;'>No colors selected. Please uncheck at least one color.</p>";
        return;
      }

      for (let i = 0; i < 3; i++) {
        const { wrapper, dice, resultText } = createDice();
        diceArea.appendChild(wrapper);

        const selected = includedConfigs[Math.floor(Math.random() * includedConfigs.length)];

        const extraX = 360 * (Math.floor(Math.random() * 2) + 1);
        const extraY = 360 * (Math.floor(Math.random() * 2) + 1);

        const finalX = selected.x + extraX;
        const finalY = selected.y + extraY;

        setTimeout(() => {
          dice.style.transform = `rotateX(${finalX}deg) rotateY(${finalY}deg)`;
          resultText.textContent = selected.color.charAt(0).toUpperCase() + selected.color.slice(1);
          resultText.style.color = selected.color;
        }, 100 * i);
      }
    }

    // Auto-roll on page load
    window.onload = rollDice;
  </script>

</body>
</html>