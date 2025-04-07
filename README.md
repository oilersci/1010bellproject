<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Time-Based Background Color (Minute Precision)</title>
  <style>
    body {
      transition: background-color 0.5s ease;
      margin: 0;
      padding: 0;
      font-family: sans-serif;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      height: 100vh;
      text-align: center;
    }

    h1 {
      font-size: 4vw;
      margin: 0.5em 0;
    }

    #currentTime, #period, #answer {
      font-size: 5vw;
      font-weight: bold;
    }

    #startButton {
      padding: 10px 20px;
      font-size: 1.5rem;
      margin-top: 1em;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h1><div id="currentTime"></div></h1>
  <h1><div id="period"></div></h1>
  <h1>May I use the restroom?</h1>
  <h1><div id="answer"></div></h1>
  <button id="startButton" onclick="enableAudio()">Start Bells</button>

  <script>
    function parseTimeToMinutes(timeStr) {
      const [hour, minute] = timeStr.split(":").map(Number);
      return hour * 60 + minute;
    }

    const intervals = [
      { start: "00:00", end: "07:05", color: "#000000", textcolor: "#ffffff", answer: "Go back to sleep.", period: "Before School" },
      { start: "07:05", end: "07:15", color: "#FDFD96", textcolor: "#000000", answer: "Be back before the bell.", period: "Passing Period"},
      { start: "07:25", end: "07:51", color: "#80EF80", textcolor: "#000000", answer: "Yes, you may, if the pass is available.", period: "1st Period" }, 
      { start: "08:01", end: "08:07", color: "#FDFD96", textcolor: "#000000", answer: "Be back before the bell.", period: "Passing Period"},
      { start: "08:17", end: "08:43", color: "#80EF80", textcolor: "#000000", answer: "Yes, you may, if the pass is available.", period: "2nd Period"}, 
      { start: "08:53", end: "08:59", color: "#FDFD96", textcolor: "#000000", answer: "Be back before the bell.", period: "Passing Period"},
      { start: "09:09", end: "09:36", color: "#80EF80", textcolor: "#000000", answer: "Yes, you may, if the pass is available.", period: "3rd Period"}, 
      { start: "09:46", end: "09:52", color: "#FDFD96", textcolor: "#000000", answer: "Be back before the bell.", period: "Passing Period"},
      { start: "10:02", end: "10:07", color: "#80EF80", textcolor: "#000000", answer: "Yes, you may, if the pass is available.", period: "Oiler Time" }, 
      { start: "10:17", end: "10:23", color: "#FDFD96", textcolor: "#000000", answer: "Be back before the bell.", period: "Passing Period"},
      { start: "10:33", end: "10:59", color: "#80EF80", textcolor: "#000000", answer: "Yes, you may, if the pass is available.", period: "4th Period" }, 
      { start: "11:09", end: "11:15", color: "#FDFD96", textcolor: "#000000", answer: "Be back before the bell.", period: "Passing Period"},
      { start: "11:56", end: "12:35", color: "#80EF80", textcolor: "#000000", answer: "Yes, you may, if the pass is available.", period: "5th Period" }, 
      { start: "12:47", end: "12:53", color: "#FDFD96", textcolor: "#000000", answer: "Be back before the bell.", period: "Passing Period"},
      { start: "13:03", end: "13:30", color: "#80EF80", textcolor: "#000000", answer: "Yes, you may, if the pass is available.", period: "6th Period" }, 
      { start: "13:40", end: "13:46", color: "#FDFD96", textcolor: "#000000", answer: "Be back before the bell.", period: "Passing Period"},
      { start: "13:56", end: "14:25", color: "#80EF80", textcolor: "#000000", answer: "Yes, you may, if the pass is available.", period: "7th Period" }, 
      { start: "14:35", end: "23:59", color: "#000000", textcolor: "#ffffff", answer: "Go home.", period: "After School" },
    ];

    let lastPlayedTime = "";
    let audioUnlocked = false;

    const bellTimes = [
      "07:05", "07:15", "08:01", "08:07", "08:53", "08:59",
      "09:46", "09:52", "10:17", "10:23", "11:09", "11:15",
      "11:45", "12:15", "12:47", "12:53", "13:40", "13:46", "14:35", "16:00"
    ];

    function enableAudio() {
      const audio = new Audio("bells.mp3");
      audio.play().then(() => {
        audio.pause();
        audioUnlocked = true;
        document.getElementById('startButton').style.display = 'none';
        console.log("Audio unlocked!");
      }).catch(err => {
        console.log("Failed to unlock audio:", err);
      });
    }

    function playBellSound() {
      if (!audioUnlocked) return;
      const audio = new Audio("https://github.com/oilersci/1010bellproject/blob/912480897c6fffaacda683412d906508c4631ae8/bells.mp3");
      audio.play().catch(error => console.log("Audio playback failed:", error));
    }

    function checkBellTimes() {
      const now = new Date();
      const hours = now.getHours().toString().padStart(2, '0');
      const minutes = now.getMinutes().toString().padStart(2, '0');
      const currentTime = `${hours}:${minutes}`;

      if (bellTimes.includes(currentTime) && currentTime !== lastPlayedTime) {
        playBellSound();
        lastPlayedTime = currentTime;
      }
    }

    function updateBackground() {
      const now = new Date();
      const timeString = now.toLocaleTimeString();
      const currentMinutes = now.getHours() * 60 + now.getMinutes();

      document.getElementById('currentTime').textContent = timeString;

      let matchedInterval = intervals.find(interval => {
        const start = parseTimeToMinutes(interval.start);
        const end = parseTimeToMinutes(interval.end);
        return currentMinutes >= start && currentMinutes < end;
      });

      if (!matchedInterval) {
        matchedInterval = { color: "#FF746C", textcolor: "#ffffff", answer: "No, The 10/10 Rule is in effect." };
      }

      document.body.style.backgroundColor = matchedInterval.color;
      document.body.style.color = matchedInterval.textcolor;
      document.getElementById('answer').textContent = matchedInterval.answer;
      document.getElementById('period').textContent = matchedInterval.period;
    }

    // Start ticking
    setInterval(checkBellTimes, 1000);
    setInterval(updateBackground, 1000);
    updateBackground();
  </script>
</body>
</html>
