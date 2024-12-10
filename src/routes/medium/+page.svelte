<script lang="ts">
  import { onMount, onDestroy } from 'svelte';

  const cardValues = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P'];

  let shuffledCards: string[] = [];
  let flippedCards: number[] = [];
  let matchedCards = new Set<number>();
  let isDisabled = false;

  let isGameOver = false;
  let isInitialReveal = true;

  let timer: number = 0;
  let interval: any;
  let isPaused: boolean = false;
  let showModal: boolean = false;
  let isGameStarted: boolean = false;

  function shuffleCards() {
    const cards = [...cardValues, ...cardValues];
    shuffledCards = cards
      .map(value => ({ value, id: Math.random() }))
      .sort((a, b) => a.id - b.id)
      .map(({ value }) => value);
  }

  function flipCard(index: number) {
    if (isDisabled || matchedCards.has(index) || flippedCards.length === 2 || isPaused) {
      return;
    }

    flippedCards = [...flippedCards, index];

    if (flippedCards.length === 2) {
      isDisabled = true;
      const [first, second] = flippedCards;

      if (shuffledCards[first] === shuffledCards[second]) {
        matchedCards.add(first);
        matchedCards.add(second);
      }

      setTimeout(() => {
        flippedCards = [];
        isDisabled = false;

        if (matchedCards.size === shuffledCards.length) {
          isGameOver = true;
          clearInterval(interval);
          showModal = true;
        }
      }, 650);
    }
  }

  function handleInitialReveal() {
    isInitialReveal = true;
    setTimeout(() => {
      flippedCards = [];
      isInitialReveal = false;
      startTimer();
    }, 200);
  }

  function startTimer() {
    if (!isPaused) {
      interval = setInterval(() => {
        timer += 1;
      }, 1000);
    }
  }

  function pauseTimer() {
    clearInterval(interval);
    isPaused = true;
    showModal = true;
  }

  function resumeTimer() {
    isPaused = false;
    showModal = false;
    startTimer();
  }

  function resetGame() {
    matchedCards.clear();
    flippedCards = [];
    isGameOver = false;
    timer = 0;
    isPaused = false;
    showModal = false;
    isGameStarted = false;
    shuffleCards();
  }

  function backToMainMenu() {
    resetGame();
    window.location.href = '/';
  }

  function startGame() {
    if (!isGameStarted) {
      isGameStarted = true;
      handleInitialReveal();
    }
  }

  function formatTime(seconds: number): string {
    const minutes = Math.floor(seconds / 60);
    const remainingSeconds = seconds % 60;
    return `${String(minutes).padStart(2, '0')}:${String(remainingSeconds).padStart(2, '0')}`;
  }

  onMount(() => {
    shuffleCards();
  });

  onDestroy(() => {
    clearInterval(interval);
  });
</script>

<style>
 :global(html, body) {
  height: 100%;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: transparent;
  overflow: hidden;
}

.memory-game {
  display: grid;
  grid-template-columns: repeat(8, 1fr); /* 8 cards per row by default */
  gap: 10px;
  width: 80vw;
  height: 80vh;
  max-width: 800px;
  max-height: 600px;
  margin: auto;
  background-color: transparent;
  position: relative;
  z-index: 1;
}

.card {
  width: 75px;
  height: 110px;
  display: flex;
  justify-content: center;
  align-items: center;
  color: black;
  font-size: 35px;
  font-weight: bold;
  border: 2px solid #797979;
  border-radius: 8px;
  cursor: pointer;
  transform: rotateY(0deg);
  transition: transform 0.3s;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
  position: relative;
  background-color: #a7b8df;
  z-index: 1;
  opacity: 0;
  animation: slideUp 0.6s ease-out forwards;
}

.flipped {
  transform: rotateY(180deg);
}

.card-text {
  backface-visibility: hidden;
  transform: rotateY(180deg);
}

.card.flipped .card-text {
  transform: rotateY(0deg);
}

.game-over {
  text-align: center;
  font-size: 24px;
  color: white;
  margin-top: 20px;
}

.timer {
  font-size: 40px;
  text-align: center;
  margin-bottom: 10px;
  font-weight: bold;
  color: white;
}

.pause-button {
  font-size: 1rem;
  padding: 6px 12px;
  color: #1e3252;
  background-color: #FFCC00;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-bottom: 10px;
}

.pause-button:hover {
  background-color: #d89d13;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  visibility: hidden;
  opacity: 0;
  transition: visibility 0.3s, opacity 0.3s;
  z-index: 2;
}

.modal.show {
  visibility: visible;
  opacity: 1;
}

.modal-content {
  background-color: #364669;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 300px;
  height: 300px;
}

.modal-content h3 {
  color: #fafafa;
  font-size: 1.2rem;
  margin-bottom: 20px;
}

.modal-button {
  display: block;
  margin: 20px auto;
  padding: 10px 20px;
  font-size: 1rem;
  color: #1e3252;
  background-color: #FFCC00;
  border: none;
  border-radius: 5px;
  margin: 10px;
  cursor: pointer;
  width: auto;
}

.modal-button:hover {
  background-color: #d89d13;
  color: rgb(51, 54, 90);
}

.start-button {
  font-size: 1rem;
  padding: 10px 20px;
  color: #1e3252;
  background-color: #FFCC00;
  border: none;
  border-radius: 5px;
  margin-bottom: 10px;
  cursor: pointer;
  opacity: 0;
  animation: slideUp 0.6s ease-out forwards;
}

.start-button:hover {
  background-color: #d89d13;
}

@keyframes slideUp {
  0% {
    transform: translateY(20px);
    opacity: 0;
  }
  100% {
    transform: translateY(0);
    opacity: 1;
  }
}

@media (max-width: 768px) {
  /* Change to 4 columns per row for screens smaller than 768px */
  .memory-game {
    grid-template-columns: repeat(4, 1fr);
    width: 90vw;
    height: 90vh;
  }

  .card {
    width: 65px;
    height: 95px;
    font-size: 28px;
  }

  .timer {
    font-size: 30px;
  }

  .pause-button {
    font-size: 0.9rem;
    padding: 5px 10px;
  }

  .start-button {
    font-size: 0.9rem;
    padding: 8px 16px;
  }
}

@media (max-width: 480px) {
  /* Change to 4 cards in a column for mobile screens */
  .memory-game {
    grid-template-columns: repeat(4, 1fr);
    width: 95vw;
    height: 95vh;
  }

  .card {
    width: 50px;
    height: 75px;
    font-size: 22px;
  }

  .timer {
    font-size: 24px;
  }

  .pause-button {
    font-size: 0.8rem;
    padding: 4px 8px;
  }

  .start-button {
    font-size: 0.8rem;
    padding: 6px 12px;
  }
}

</style>

<div class="timer">
  {formatTime(timer)}
</div>

{#if !isGameStarted}
  <button class="start-button" on:click={startGame}>Start Game</button>
{/if}

{#if isGameStarted}
  <button class="pause-button" on:click={pauseTimer}>Pause</button>
{/if}

<div class="modal {showModal ? 'show' : ''}">
  <div class="modal-content {isGameOver ? 'game-over' : ''}">
    {#if isGameOver}
      <p>Congratulations! You won in {formatTime(timer)}!</p>
      <button class="modal-button" on:click={backToMainMenu}>Back to the Main Menu</button>
    {:else}
      <h3>Game Paused</h3>
      <button class="modal-button" on:click={resumeTimer}>Resume Game</button>
      <button class="modal-button" on:click={backToMainMenu}>Back to Main Menu</button>
    {/if}
  </div>
</div>

<div class="memory-game">
  {#each shuffledCards as card, index}
    <button
      class="card {flippedCards.includes(index) || matchedCards.has(index) || isInitialReveal ? 'flipped' : ''}"
      on:click={() => flipCard(index)}
      aria-label={"Flip card " + card}
    >
      <div class="card-text">{card}</div>
    </button>
  {/each}
</div>
