<script lang="ts">
    type OptionAction = "start" | "quit";
  
    interface Option {
      name: string;
      action: OptionAction;
    }
  
    let options: Option[] = [
      { name: "Start Game", action: "start" },
      { name: "Quit", action: "quit" },
    ];
  
    let showModal = false;
    let difficultyModal = false;
  
    let playSoundEffect: HTMLAudioElement;
    let quitSoundEffect: HTMLAudioElement;
    let modalButtonSound: HTMLAudioElement;
  
    function handleOption(option: OptionAction): void {
      if (option === "start") {
        playSoundEffect.play();
        difficultyModal = true; // Show difficulty modal when play is clicked
      } else if (option === "quit") {
        quitSoundEffect.play();
        showModal = true;
      }
    }
  
    function handleQuitConfirmation(confirm: boolean): void {
      modalButtonSound.play();
  
      if (confirm) {
        alert("Goodbye!");
        window.close();
      }
      showModal = false;
    }
  
    function handleDifficultySelection(difficulty: string, event: MouseEvent): void {
      event.preventDefault(); // Prevent default anchor tag behavior
      modalButtonSound.play(); // Play the sound before navigating
      // Wait for sound to finish before navigating
      setTimeout(() => {
        window.location.href = `/${difficulty}`;
      }, 500); // Adjust time based on sound duration
    }
  </script>
  
  <style>
    :global(html, body) {
      height: 100%;
      margin: 0;
      overflow: hidden;
    }
  
    main {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      height: 100vh;
      color: #1e293b;
      font-family: 'Courier New', monospace;
      background: transparent;
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }
  
    /* Unified Slide Up Animation for both h2 and buttons */
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
  
    /* Logo Animation */
    img {
      width: 40%;
      max-width: 450px;
      height: auto;
      margin-top: -50px;
      margin-bottom: 20px;
      transition: width 0.3s ease; /* Smooth transition for resizing */
      animation: slideUp 1s ease-out forwards;
    }
  
    /* Heading Animation */
    h1 {
      font-size: 1.8rem;
      margin-bottom: 10px;
      color: #fafafa;
      font-weight: bold;
      text-align: center;
      animation: slideUp 1s ease-out forwards;
    }
  
    /* Same animation for h2 and buttons */
    h2, button {
      animation: slideUp 1s ease-out forwards;
    }
  
    /* Button Styling */
    button {
      margin: 10px;
      padding: 12px 25px;
      font-size: 1rem;
      color: #1e293b;
      background-color: #FFCC00;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      transition: 0.3s;
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }
  
    button:hover {
      background-color: #d89d13;
      color: rgb(51, 54, 90);
    }
  
    button:nth-child(1) {
      animation-delay: 0.6s; /* Delay the 'Play Now' button */
    }
  
    button:nth-child(2) {
      animation-delay: 0.7s; /* Delay the 'Quit' button */
    }
  
    /* Modal styles */
    .modal {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.5);
      display: flex;
      justify-content: center;
      align-items: center;
      visibility: hidden;
      opacity: 0;
      transition: visibility 0s, opacity 0.3s ease-in-out;
    }
  
    .modal.show {
      visibility: visible;
      opacity: 1;
    }
  
    .modal-content {
      background: #364669;
      padding: 20px;
      border-radius: 10px;
      text-align: center;
      width: 90%;
      max-width: 400px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }
  
    .modal-content h2 {
      color: #fafafa;
      font-size: 1.2rem;
      margin-bottom: 20px;
    }
  
    /* Quit Confirmation Modal (Vertical buttons) */
    .modal.show .modal-content .modal-buttons.quit {
      display: flex;
      flex-direction: row; /* Stack buttons vertically */
      align-items: center;
      gap: 15px; /* Space between buttons */
    }
  
    .modal-buttons button {
      margin: 0;
      padding: 10px 20px;
      font-size: 1rem;
      color: #1e3252;
      background-color: #FFCC00;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      width: auto;
      max-width: 200px;
      text-align: center;
    }
  
    .modal-buttons button:hover {
      background-color: #d89d13;
    }
  
    /* Difficulty Selection Modal (Horizontal buttons) */
    .modal.show .modal-content .modal-buttons {
      display: flex;
      justify-content: center;
      align-items: center;
      gap: 15px;
      flex-direction: column;
    }
  
    /* Styling for the difficulty selection buttons */
    button.difficulty-button {
      text-decoration: none; /* No need for <a> tag */
    }
  
    button.difficulty-button:hover {
      background-color: #d89d13;
      color: rgb(51, 54, 90);
    }
  
    audio {
      display: none;
    }
  </style>
  
  <main>
    <audio autoplay loop>
      <source src="./sounds/bgMusic.mp3" type="audio/mpeg" />
      Your browser does not support the audio element.
    </audio>
  
    <audio bind:this={playSoundEffect}>
      <source src="./sounds/click.mp3" type="audio/mpeg" />
    </audio>
    <audio bind:this={quitSoundEffect}>
      <source src="./sounds/click.mp3" type="audio/mpeg" />
    </audio>
    <audio bind:this={modalButtonSound}>
      <source src="./sounds/click.mp3" type="audio/mpeg" />
    </audio>
  
    <img src="./images/Logo.webp" alt="Logo" />
    <h1>Main Menu</h1>
    {#each options as option}
      <button on:click={() => handleOption(option.action)}>{option.name}</button>
    {/each}
  
    <!-- Main Quit Confirmation Modal -->
    <div class="modal {showModal ? 'show' : ''}">
      <div class="modal-content">
        <h2>Are you sure you want to quit?</h2>
        <div class="modal-buttons quit">
          <button on:click={() => handleQuitConfirmation(true)}>Yes</button>
          <button on:click={() => handleQuitConfirmation(false)}>No</button>
        </div>
      </div>
    </div>
  
    <!-- Difficulty Modal -->
    <div class="modal {difficultyModal ? 'show' : ''}">
      <div class="modal-content">
        <h2>Select Difficulty</h2>
        <div class="modal-buttons">
          <button class="difficulty-button" on:click={(e) => handleDifficultySelection("easy", e)}>Easy</button>
          <button class="difficulty-button" on:click={(e) => handleDifficultySelection("medium", e)}>Medium</button>
          <button class="difficulty-button" on:click={(e) => handleDifficultySelection("hard", e)}>Hard</button>
        </div>
      </div>
    </div>
  </main>
  