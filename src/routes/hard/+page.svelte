<script lang="ts">
    let inGame = false;  // Track if the game has started
    let countdown = 3;  // Countdown starting at 3
    let countdownInterval: ReturnType<typeof setInterval> | null = null;  // Proper type
    let countdownSound: HTMLAudioElement;  // Sound for the countdown
    let backgroundMusic: HTMLAudioElement;  // Background music element
  
    // Audio for button clicks
    let buttonSound: HTMLAudioElement;
  
    // Start the countdown and game
    function startGame() {
      inGame = true;
      countdown = 3;  // Reset countdown to 3 before starting
      countdownInterval = setInterval(() => {
        if (countdown > 0) {
          countdown--;
          playCountdownSound(); // Play countdown sound every second
        } else {
          clearInterval(countdownInterval!); // Stop the countdown when it reaches 0
        }
      }, 1000);  // Update every second
      
      // Fade out background music
      fadeOutBackgroundMusic();
    }
  
    // Play the countdown sound
    function playCountdownSound() {
      countdownSound.play();
    }
  
    // Fade out the background music
    function fadeOutBackgroundMusic() {
      let fadeDuration = 3000; // Duration of the fade in milliseconds
      let fadeInterval = 50; // Interval to decrease volume
      let fadeStep = 1 / (fadeDuration / fadeInterval); // Amount to decrease volume each step
      let currentVolume = backgroundMusic.volume;
  
      let fade = setInterval(() => {
        currentVolume -= fadeStep;
        if (currentVolume <= 0) {
          clearInterval(fade); // Stop fading when volume reaches 0
          backgroundMusic.pause(); // Pause the background music
          backgroundMusic.currentTime = 0; // Reset to the start
        }
        backgroundMusic.volume = Math.max(currentVolume, 0); // Ensure volume doesn't go below 0
      }, fadeInterval);
    }
  
    // Play the sound effect
    function playButtonSound() {
      buttonSound.play();
    }
  
    // Handle the "Back to Main Menu" click with sound and navigation
    function handleBackToMainMenu(event: Event) {
      event.preventDefault(); // Prevent immediate navigation
      playButtonSound(); // Play the sound first
      setTimeout(() => {
        window.location.href = '/'; // Manually navigate after sound finishes
      }, 300); // Wait for the sound to finish (300ms delay)
    }
  </script>
  
  <svelte:head>
    <!-- Font import -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous">
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@100;400;700&display=swap" rel="stylesheet">
  </svelte:head>
  
  <main>
    <!-- Background music with autoplay and loop -->
    <audio bind:this={backgroundMusic} autoplay loop>
      <source src="./sounds/bgMusic.mp3" type="audio/mpeg" />
      Your browser does not support the audio element.
    </audio>
  
    <!-- Audio for button click sound -->
    <audio bind:this={buttonSound}>
      <source src="./sounds/click.mp3" type="audio/mpeg" />
    </audio>
  
    <!-- Audio for countdown sound -->
    <audio bind:this={countdownSound}>
      <source src="./sounds/tut.mp3" type="audio/mpeg" />
    </audio>
    
    {#if !inGame}
      <!-- Main Menu -->
      <div class="centered">
        <div class="button-container">
          <!-- Heading added above the button -->
          <h1 class="main-heading">50 Cards</h1> 
          <button class="modal-button start" on:click={() => { playButtonSound(); startGame(); }}>Play Now</button>
          <a href="./" class="modal-button bottom" on:click={handleBackToMainMenu}>Back to Main Menu</a>
        </div>
      </div>
    {:else}
      <!-- Game Screen with Countdown -->
      <div class="centered">
        {#if countdown > 0}
          <div class="countdown-container">
            <h1 class="countdown">{countdown}</h1>
          </div>
        {:else}
          <h1 class="start-text">START!</h1>
        {/if}
      </div>
    {/if}
  </main>
  
  <style>
    /* Prevent scrolling */
    :global(html, body) {
      height: 100%; 
      overflow: hidden;
      margin: 0;
      font-family: 'Poppins', sans-serif; /* Global font applied */
    }
  
    main {
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      font-family: 'Poppins', sans-serif; /* Apply Poppins font */
    }
  
    .centered {
      text-align: center;
      display: flex;
      flex-direction: column; 
      justify-content: center;
      height: 100%;
    }
  
    .button-container {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      gap: 20px;  /* Reduced space between elements in the container */
    }
  
    .main-heading {
      font-size: 7rem;
      font-family: 'Courier New', monospace;
      color: white;
      margin-bottom: 10px;  /* Reduced margin to decrease space between heading and button */
      font-weight: bold;
    }
  
    .modal-button {
      margin: 0;
      padding: 15px 30px; /* Increase padding for larger buttons */
      font-size: 1.2rem;  /* Increase font size for better visibility */
      font-family: 'Courier New', monospace;
      color: #1e3252;
      background-color: #FFCC00;
      border: none;
      border-radius: 10px; /* Rounder corners for a modern look */
      cursor: pointer;
      text-align: center;
      text-decoration: none; /* Remove underline for links */
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1); /* Subtle shadow for depth */
      transition: background-color 0.3s, transform 0.2s; /* Smooth hover effect */
    }
  
    .modal-button:hover {
      background-color: #d89d13;
      transform: scale(1.05); /* Slight zoom effect on hover */
    }
  
    .countdown-container {
      padding: 20px;
      display: inline-block;
    }
  
    .countdown {
      font-size: 200px;
      font-weight: bold;
      color: white;
      margin: 0;
      text-shadow: 
        2px 2px 0 red,
        -2px -2px 0 red,
        2px -2px 0 red,
        -2px 2px 0 red;
      animation: countdown-animation 1s ease-in-out;
    }
  
    .start-text {
      font-size: 140px;
      color: white;
      margin-bottom: 20px;
    }
  
    @keyframes countdown-animation {
      0% { transform: scale(1); }
      50% { transform: scale(1.2); }
      100% { transform: scale(1); }
    }
  </style>
  