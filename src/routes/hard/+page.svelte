<script lang="ts">
    import { onMount, onDestroy } from 'svelte';
  
    // Define 27 unique pairs of cards (54 cards total)
    const cardValues = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z', '1', '2'];
  
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
  
    // Reference to the audio element for button sound
  let playSound: HTMLAudioElement;

    // Shuffle the cards by randomizing their order
    function shuffleCards() {
      const cards = [...cardValues, ...cardValues]; // Duplicate for pairs
      shuffledCards = cards
        .map(value => ({ value, id: Math.random() }))
        .sort((a, b) => a.id - b.id)
        .map(({ value }) => value);
    }
  
    // Play button click sound
  function playClickSound() {
    if (playSound) {
      playSound.currentTime = 0; // Reset the sound to start from the beginning
      playSound.play();
    }
  }
    // Flip a card and check for matches
    function flipCard(index: number) {
      if (isDisabled || matchedCards.has(index) || flippedCards.length === 2 || isPaused) {
        return;
      }
  
      flippedCards = [...flippedCards, index];
    playClickSound();  // Play sound when a card is flipped
  
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
            showModal = true;  // Show the modal when the game is over
          }
        }, 1000);
      }
    }
  
    // Handle the initial reveal of the cards (before game starts)
    function handleInitialReveal() {
      isInitialReveal = true;
      setTimeout(() => {
        flippedCards = [];
        isInitialReveal = false;
        startTimer(); // Start the timer after initial reveal
      }, 200); // Reveal the cards for 2 seconds before starting the timer
    }
  
    // Start the timer when the game begins
    function startTimer() {
      if (!isPaused) {
        interval = setInterval(() => {
          timer += 1;
        }, 1000);
      }
    }
  
    // Pause the timer and show the modal
    function pauseTimer() {
      clearInterval(interval);
      isPaused = true;
      showModal = true;
      playClickSound();
    }
  
    // Resume the timer and hide the modal
    function resumeTimer() {
      isPaused = false;
      showModal = false;
      startTimer();
      playClickSound();
    }
  
    // Reset the game
    function resetGame() {
      matchedCards.clear();
      flippedCards = [];
      isGameOver = false;
      timer = 0;
      isPaused = false;
      showModal = false;
      isGameStarted = false; // Mark the game as not started
      shuffleCards(); // Shuffle cards again when the game is reset
      playClickSound();
    }
  
    function backToMainMenu() {
      resetGame();
      window.location.href = '/';  // Redirect to home page
    }
  
    // Start the game (trigger initial reveal and timer)
    function startGame() {
      if (!isGameStarted) {
        isGameStarted = true;
        handleInitialReveal(); // Reveal cards initially
        playClickSound();
      }
    }
  
    // Format time in MM:SS format
    function formatTime(seconds: number): string {
      const minutes = Math.floor(seconds / 60);
      const remainingSeconds = seconds % 60;
      return `${String(minutes).padStart(2, '0')}:${String(remainingSeconds).padStart(2, '0')}`;
    }
  
    onMount(() => {
      shuffleCards(); // Shuffle cards only once when the component is mounted
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
      grid-template-columns: repeat(14, 1fr); /* 14 columns for 4 cards per vertical row */
      gap: 10px;
      width: 100vw;
      height: 80vh;
      max-width: 2700px;
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
    }
  
    .start-button:hover {
      background-color: #d89d13;
    }
  </style>
  
  <!-- Timer display -->
  <div class="timer">
    {formatTime(timer)}
  </div>
  
  <!-- Start button (only visible when the game hasn't started) -->
  {#if !isGameStarted}
  <button class="start-button" on:click={startGame}>Start Game</button>
  {/if}
  
  <!-- Pause button (only visible after game starts) -->
  {#if isGameStarted}
  <button class="pause-button" on:click={pauseTimer}>Pause</button>
  {/if}
  
  <!-- Modal for pause and game over state -->
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
  
  <!-- Memory game grid -->
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
  
  <audio bind:this={playSound}>
    <source src="./sounds/click.mp3" type="audio/mpeg" />
  </audio>