<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>랜덤 낙서 초상화</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    body {
      width: 100vw;
      height: 100vh;
      overflow: hidden;
      font-family: Pretendard, Arial, sans-serif;
      background: #111;
    }

    /* 전체 배경 */
    .page {
      position: relative;
      width: 100vw;
      height: 100vh;
      background-image: url("./images/background.jpg");
      background-size: cover;
      background-position: center;
      background-repeat: no-repeat;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: clamp(24px, 6vw, 100px);
      padding: 40px;
    }

    /* 초상화 영역 */
    .player-area {
      width: min(36vw, 420px);
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 18px;
    }

    .portrait-box {
      position: relative;
      width: 100%;
      aspect-ratio: 3 / 4;
      border-radius: 28px;
      overflow: hidden;
      background: rgba(255, 255, 255, 0.15);
      border: 4px solid rgba(255, 255, 255, 0.85);
      box-shadow: 0 18px 45px rgba(0, 0, 0, 0.35);
    }

    .portrait-box img.portrait {
      width: 100%;
      height: 100%;
      object-fit: cover;
      display: block;
      user-select: none;
      pointer-events: none;
    }

    /* 붙는 낙서 이미지 */
    .doodle {
      position: absolute;
      width: 26%;
      height: auto;
      transform-origin: center;
      user-select: none;
      pointer-events: none;
      animation: pop 0.18s ease-out;
    }

    @keyframes pop {
      from {
        transform: scale(0.5) rotate(var(--rotate));
        opacity: 0;
      }
      to {
        transform: scale(1) rotate(var(--rotate));
        opacity: 1;
      }
    }

    .penalty-button {
      cursor: pointer;
      border: none;
      border-radius: 999px;
      padding: 14px 34px;
      font-size: clamp(18px, 2vw, 26px);
      font-weight: 900;
      color: #fff;
      background: linear-gradient(135deg, #ff4fa3, #7c5cff);
      box-shadow: 0 10px 24px rgba(0, 0, 0, 0.3);
      transition: transform 0.15s ease, filter 0.15s ease;
    }

    .penalty-button:hover {
      transform: translateY(-3px) scale(1.03);
      filter: brightness(1.08);
    }

    .penalty-button:active {
      transform: scale(0.96);
    }

    .name-label {
      color: #fff;
      font-size: clamp(20px, 2.2vw, 32px);
      font-weight: 900;
      text-shadow: 0 3px 12px rgba(0, 0, 0, 0.55);
    }

    .control-panel {
      position: absolute;
      right: 24px;
      bottom: 24px;
      display: flex;
      gap: 10px;
      z-index: 10;
    }

    .small-button {
      cursor: pointer;
      border: 1px solid rgba(255, 255, 255, 0.65);
      border-radius: 999px;
      padding: 10px 16px;
      color: #fff;
      background: rgba(0, 0, 0, 0.45);
      backdrop-filter: blur(8px);
      font-weight: 700;
    }

    @media (max-width: 768px) {
      .page {
        flex-direction: column;
        justify-content: flex-start;
        overflow-y: auto;
      }

      .player-area {
        width: min(78vw, 360px);
      }

      .control-panel {
        position: fixed;
      }
    }
  </style>
</head>
<body>
  <main class="page">
    <!-- A 플레이어 -->
    <section class="player-area">
      <div class="name-label">A</div>
      <div class="portrait-box" id="portraitA">
        <img class="portrait" src="./images/portrait-a.png" alt="A 초상화" />
      </div>
      <button class="penalty-button" onclick="addRandomDoodle('portraitA')">A 벌칙</button>
    </section>

    <!-- B 플레이어 -->
    <section class="player-area">
      <div class="name-label">B</div>
      <div class="portrait-box" id="portraitB">
        <img class="portrait" src="./images/portrait-b.png" alt="B 초상화" />
      </div>
      <button class="penalty-button" onclick="addRandomDoodle('portraitB')">B 벌칙</button>
    </section>

    <div class="control-panel">
      <button class="small-button" onclick="clearDoodles('portraitA')">A 낙서 초기화</button>
      <button class="small-button" onclick="clearDoodles('portraitB')">B 낙서 초기화</button>
      <button class="small-button" onclick="clearAllDoodles()">전체 초기화</button>
    </div>
  </main>

  <script>
    // 준비된 낙서 이미지 10종
    // 실제 파일명에 맞춰 images 폴더에 넣어주세요.
    const doodleImages = [
      "./images/doodle-01.png",
      "./images/doodle-02.png",
      "./images/doodle-03.png",
      "./images/doodle-04.png",
      "./images/doodle-05.png",
      "./images/doodle-06.png",
      "./images/doodle-07.png",
      "./images/doodle-08.png",
      "./images/doodle-09.png",
      "./images/doodle-10.png"
    ];

    function getRandomItem(array) {
      return array[Math.floor(Math.random() * array.length)];
    }

    function randomBetween(min, max) {
      return Math.random() * (max - min) + min;
    }

    function addRandomDoodle(targetId) {
      const target = document.getElementById(targetId);
      const doodle = document.createElement("img");

      const randomImage = getRandomItem(doodleImages);
      const randomLeft = randomBetween(5, 72);   // 초상화 안쪽 랜덤 X 위치
      const randomTop = randomBetween(5, 72);    // 초상화 안쪽 랜덤 Y 위치
      const randomRotate = randomBetween(-28, 28);
      const randomSize = randomBetween(18, 34);  // 낙서 크기 랜덤

      doodle.src = randomImage;
      doodle.alt = "랜덤 낙서";
      doodle.className = "doodle";
      doodle.style.left = `${randomLeft}%`;
      doodle.style.top = `${randomTop}%`;
      doodle.style.width = `${randomSize}%`;
      doodle.style.setProperty("--rotate", `${randomRotate}deg`);
      doodle.style.transform = `rotate(${randomRotate}deg)`;

      // 누를 때마다 새 낙서가 기존 낙서 위에 계속 고정됨
      target.appendChild(doodle);
    }

    function clearDoodles(targetId) {
      const target = document.getElementById(targetId);
      target.querySelectorAll(".doodle").forEach((doodle) => doodle.remove());
    }

    function clearAllDoodles() {
      clearDoodles("portraitA");
      clearDoodles("portraitB");
    }
  </script>
</body>
</html>
