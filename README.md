<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Baccarat Card Counter</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #1b1b1b;
      color: white;
      text-align: center;
      padding: 20px;
    }

    h1 {
      margin-bottom: 10px;
    }

    .deck-info {
      margin-bottom: 10px;
    }

    .count-info {
      margin-bottom: 20px;
      font-size: 18px;
    }

    .controls {
      margin-bottom: 20px;
    }

    .card-grid {
      display: grid;
      grid-template-columns: repeat(13, 60px);
      gap: 10px;
      justify-content: center;
      margin-bottom: 30px;
    }

    .card {
      width: 60px;
      height: 80px;
      border: 2px solid white;
      border-radius: 8px;
      display: flex;
      justify-content: center;
      align-items: center;
      background-color: #282828;
      cursor: pointer;
      font-size: 16px;
    }

    .card.clicked {
      background-color: #c0392b;
      text-decoration: line-through;
      color: #eee;
    }

    .suit-select {
      margin-bottom: 20px;
    }

    button {
      padding: 10px 20px;
      margin: 5px;
      font-size: 16px;
      cursor: pointer;
      border-radius: 5px;
      border: none;
      background-color: #3498db;
      color: white;
    }

    button:hover {
      background-color: #2980b9;
    }
  </style>
</head>
<body>
  <h1>🃏 Baccarat Card Counter</h1>
  <p class="deck-info">6 decks: <span id="remaining-count">312</span> cards remaining</p>
  <p class="count-info">Running Count: <span id="running-count">0</span></p>

  <div class="controls">
    <label for="suit">Choose suit (optional):</label>
    <select id="suit">
      <option value="">Any</option>
      <option value="♠">Spades</option>
      <option value="♥">Hearts</option>
      <option value="♦">Diamonds</option>
      <option value="♣">Clubs</option>
    </select>

    <button id="reset-hand">♻️ Reset Hand</button>
  </div>

  <div class="card-grid" id="cardGrid"></div>

  <script>
    const suits = ['♠', '♥', '♦', '♣'];
    const ranks = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];

    const cardGrid = document.getElementById('cardGrid');
    const suitSelect = document.getElementById('suit');
    const remainingCount = document.getElementById('remaining-count');
    const runningCountDisplay = document.getElementById('running-count');
    const resetHandBtn = document.getElementById('reset-hand');

    let totalCards = 6 * 52;
    let runningCount = 0;

    const clickedCards = new Set();     // Cards removed from shoe
    const currentHand = new Set();      // Cards clicked this hand

    function getCardValue(rank) {
      const plusOne = ['2', '3', '4', '5', '6', '7'];
      const minusOne = ['10', 'J', 'Q', 'K', 'A'];
      if (plusOne.includes(rank)) return 1;
      if (minusOne.includes(rank)) return -1;
      return 0;
    }

    function createCards() {
      cardGrid.innerHTML = '';
      for (let rank of ranks) {
        for (let suit of suits) {
          const cardId = `${rank}${suit}`;
          const card = document.createElement('div');
          card.className = 'card';
          card.innerText = `${rank}${suit}`;
          card.dataset.rank = rank;
          card.dataset.cardId = cardId;

          card.addEventListener('click', () => {
            const value = getCardValue(rank);

            if (!card.classList.contains('clicked')) {
              card.classList.add('clicked');
              if (!clickedCards.has(cardId)) {
                totalCards--;
                runningCount += value;
                clickedCards.add(cardId);
              }
              currentHand.add(cardId);
            } else if (currentHand.has(cardId)) {
              card.classList.remove('clicked');
              currentHand.delete(cardId);
              if (!clickedCards.has(cardId)) return;
              runningCount -= value;
              totalCards++;
              clickedCards.delete(cardId);
            }

            remainingCount.innerText = totalCards;
            runningCountDisplay.innerText = runningCount;
          });

          cardGrid.appendChild(card);
        }
      }
    }

    suitSelect.addEventListener('change', () => {
      const selectedSuit = suitSelect.value;
      const cards = document.querySelectorAll('.card');
      cards.forEach(card => {
        card.style.display = selectedSuit === "" || card.innerText.endsWith(selectedSuit) ? "flex" : "none";
      });
    });

    resetHandBtn.addEventListener('click', () => {
      currentHand.forEach(cardId => {
        const cardEl = document.querySelector(`.card[data-card-id='${cardId}']`);
        if (cardEl && cardEl.classList.contains('clicked')) {
          cardEl.classList.remove('clicked');
        }
        currentHand.delete(cardId);
        // Do NOT remove from clickedCards or adjust count — card already counted
      });
    });

    createCards();
  </script>
</body>
</html>