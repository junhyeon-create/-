<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <title>💎 바카라 게임</title>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      background: radial-gradient(circle, #1f0037, #000);
      color: #fff;
      text-align: center;
      padding: 20px;
    }
    h1 {
      font-size: 48px;
      color: gold;
      margin-bottom: 10px;
    }
    .card {
      display: inline-block;
      margin: 5px;
      padding: 15px;
      width: 60px;
      height: 90px;
      border-radius: 10px;
      background: white;
      color: black;
      font-size: 24px;
      font-weight: bold;
      box-shadow: 2px 2px 10px #000;
      transform: scale(0);
      animation: popIn 0.4s forwards;
    }
    @keyframes popIn {
      to {
        transform: scale(1);
      }
    }
    button {
      margin: 8px;
      padding: 12px 20px;
      font-size: 18px;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      background: gold;
      color: black;
      font-weight: bold;
    }
    .section { margin-top: 30px; }
    .bet-btn {
      background: linear-gradient(to right, #ff6a00, #ee0979);
      color: white;
    }
    .player-area, .banker-area {
      display: inline-block;
      width: 40%;
      vertical-align: top;
    }
    .tie-area {
      margin-top: 10px;
      font-size: 20px;
      color: lime;
    }
    .player-area h2 { color: #00BFFF; }
    .banker-area h2 { color: #FF4040; }
  </style>
</head>
<body>
  <h1>💎 바카라 게임</h1>
  <p>💰 잔고: <span id="balance">1000</span> 코인</p>
  <p>💸 현재 베팅: <span id="current-bet">없음</span></p>

  <div class="section">
    <button class="bet-btn" onclick="placeBet('player')">플레이어에 100 베팅</button>
    <button class="bet-btn" onclick="placeBet('banker')">뱅커에 100 베팅</button>
    <button class="bet-btn" onclick="placeBet('tie')">타이에 100 베팅</button>
  </div>

  <div class="section">
    <div class="player-area">
      <h2>🧍‍♂️ 플레이어</h2>
      <div id="player-cards"></div>
      <p>점수: <span id="player-score">0</span></p>
    </div>

    <div class="banker-area">
      <h2>🏦 뱅커</h2>
      <div id="banker-cards"></div>
      <p>점수: <span id="banker-score">0</span></p>
    </div>
  </div>

  <div class="tie-area">
    <h2 id="result"></h2>
  </div>

  <div class="section">
    <button onclick="deal()">카드 나누기</button>
    <button onclick="restart()">다시하기</button>
  </div>

  <script>
    let deck = [], balance = 1000, betType = null;

    const cardValues = {
      'A': 1, '2': 2, '3': 3, '4': 4, '5': 5,
      '6': 6, '7': 7, '8': 8, '9': 9,
      '10': 0, 'J': 0, 'Q': 0, 'K': 0
    };

    function createDeck() {
      const suits = ['♠', '♥', '♣', '♦'];
      const ranks = Object.keys(cardValues);
      deck = [];
      for (let suit of suits) {
        for (let rank of ranks) {
          deck.push({ rank, suit });
        }
      }
      deck.sort(() => Math.random() - 0.5);
    }

    function getScore(hand) {
      let total = hand.reduce((sum, card) => sum + cardValues[card.rank], 0);
      return total % 10;
    }

    function renderCards(hand, elementId) {
      document.getElementById(elementId).innerHTML = hand.map(card =>
        `<div class="card">${card.rank}${card.suit}</div>`).join('');
    }

    function placeBet(type) {
      if (balance < 100) return alert("잔고 부족!");
      if (betType) return alert("이미 베팅했습니다!");
      betType = type;
      balance -= 100;
      document.getElementById("current-bet").textContent = type.toUpperCase();
      document.getElementById("balance").textContent = balance;
    }

    function deal() {
      if (!betType) return alert("먼저 베팅하세요!");

      createDeck();
      let player = [deck.pop(), deck.pop()];
      let banker = [deck.pop(), deck.pop()];

      let playerScore = getScore(player);
      let bankerScore = getScore(banker);

      let playerThird = null;
      if (playerScore < 8 && bankerScore < 8) {
        if (playerScore <= 5) {
          playerThird = deck.pop();
          player.push(playerThird);
        }

        const thirdValue = playerThird ? cardValues[playerThird.rank] : null;

        if (
          (bankerScore <= 2) ||
          (bankerScore === 3 && thirdValue !== 8) ||
          (bankerScore === 4 && thirdValue >= 2 && thirdValue <= 7) ||
          (bankerScore === 5 && thirdValue >= 4 && thirdValue <= 7) ||
          (bankerScore === 6 && thirdValue >= 6 && thirdValue <= 7)
        ) {
          if (!(bankerScore === 3 && thirdValue === 8)) {
            banker.push(deck.pop());
          }
        }
      }

      playerScore = getScore(player);
      bankerScore = getScore(banker);

      renderCards(player, "player-cards");
      renderCards(banker, "banker-cards");
      document.getElementById("player-score").textContent = playerScore;
      document.getElementById("banker-score").textContent = bankerScore;

      let result = "";
      if (playerScore > bankerScore) {
        result = "플레이어 승!";
        if (betType === "player") balance += 200;
      } else if (playerScore < bankerScore) {
        result = "뱅커 승!";
        if (betType === "banker") balance += 195;
      } else {
        result = "타이!";
        if (betType === "tie") balance += 900;
      }

      document.getElementById("result").textContent = `🎉 ${result}`;
      document.getElementById("balance").textContent = balance;
      betType = null;
      document.getElementById("current-bet").textContent = "없음";
    }

    function restart() {
      document.getElementById("player-cards").innerHTML = "";
      document.getElementById("banker-cards").innerHTML = "";
      document.getElementById("player-score").textContent = "0";
      document.getElementById("banker-score").textContent = "0";
      document.getElementById("result").textContent = "";
      betType = null;
      document.getElementById("current-bet").textContent = "없음";
    }

    createDeck();
  </script>
</body>
</html>
