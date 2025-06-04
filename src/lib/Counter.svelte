<script>
  import { Confetti } from "svelte-confetti"

  let currentSlide = $state(0);
  let userResponse = $state('');
  let foodClicked = $state(new Set());
  let isTransitioning = $state(false);
  let cakesEaten = $state(0);
  let rocketLaunched = $state(false);
  
  const slides = [
    { text: "Hi Elena! 👋 (turn on sound)", type: "text" },
    { text: "This is Kai", type: "image", image: "https://i.imgur.com/d0bCvXi.jpeg" },
    { text: "Happy Birthday!", type: "text" },
    { text: "26, wow that's old", type: "image", image: "https://i.imgur.com/0zFdc3P.png" },
    { text: "It's always a shame that I am unable to be with you on your birthday", type: "text" },
    { text: "😭", type: "emoji" },
    { text: "But I am sure you are having a great time over there surrounded by your friends and family", type: "text" },
    { text: "and also eating good food 😋", type: "food", instruction: "click to eat food" },
    { text: "I love you so much❤️", type: "text" },
    { text: "like this much", type: "massive" },
    { text: "I also like ur titties", type: "tiny" },
    { text: "anyway", type: "text" },
    { text: "cake break!", type: "text" },
    { text: "eat the cake!", type: "cake-eating" },
    { text: "bro, you just ate 5 cakes", type: "text" },
    { text: "gawd dayum!", type: "text" },
    { text: "congrats on your 26th adventure around the sun", type: "orbit", elenaImage: "https://i.imgur.com/AUblpxy.png" },
    { text: "Subway surfers adhd break", type: "gif" },
    { text: "whats your birthday wish for 26?", type: "input" },
    { text: `Your gift this year is a ${userResponse}!!!`, type: "response" },
    { text: "maybe.", type: "text" },
    { text: "okok for real", type: "text" },
    { text: "drumroll please", type: "drumroll" },
    { text: "On this years USA trip...", type: "text" },
    { text: "We will watch HOUNDMOUTH in New Jersey!", type: "image", image: "https://www.highroadtouring.com/wp-content/uploads/2012/09/Houndmouth-main3-870x580.png" },
    { text: "and stay in a nice hotel", type: "image", image: "https://images.unsplash.com/photo-1566073771259-6a8506099945?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80" },
    { text: "and get massages", type: "image", image: "https://images.unsplash.com/photo-1544161515-4ab6ce6db874?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80" },
    { text: "and bang bang boom boom", type: "small" },
    { text: "GET HYYYYPPEEE", type: "text" },
    { text: "I love you Elena❤️", type: "text" },
    { text: "I wish I could be there", type: "text" },
    { text: "but me run business", type: "text" },
    { text: "ur hot", type: "text" },
    { text: "thanks for listening to my ted talk", type: "text" },
    { text: "give me 5 stars on google.", type: "text" },
    { text: "- Kai", type: "text" },
    { text: "🚀", type: "rocket", image: "https://i.imgur.com/AUblpxy.png" },
  ];

  const foods = [
    { emoji: "🍕", name: "pizza" },
    { emoji: "🍔", name: "burger" },
    { emoji: "🍰", name: "cake" },
    { emoji: "🍣", name: "sushi" }
  ];

  function nextSlide() {
    const slide = slides[currentSlide];
    
    // Special conditions for certain slides
    if (slide.type === "food" && foodClicked.size < foods.length) {
      return; // Can't proceed until all food is clicked
    }
    
    if (slide.type === "input" && !userResponse.trim()) {
      return; // Can't proceed without response
    }

    if (slide.type === "cake-eating" && cakesEaten < 5) {
      return; // Can't proceed until 5 cakes are eaten
    }

    if (currentSlide < slides.length - 1) {
      isTransitioning = true;
      setTimeout(() => {
        currentSlide++;
        isTransitioning = false;
      }, 210);
    }
  }

  function clickFood(foodName) {
    foodClicked = new Set([...foodClicked, foodName]);
  }

  function playDrumroll() {
    // Play drumroll sound from assets
    const audio = new Audio('/src/assets/drumroll.wav'); // Adjust the filename if different
    audio.volume = 0.5; // Adjust volume as needed
    audio.play().catch(err => console.log('Audio play failed:', err));
    
    setTimeout(nextSlide, 2000);
  }

  function handleInput(event) {
    if (event.key === 'Enter' && userResponse.trim()) {
      nextSlide();
    }
  }

  function eatCake() {
    cakesEaten++;
    if (cakesEaten >= 5) {
      setTimeout(() => {
        nextSlide();
      }, 500);
    }
  }

  function playHoundmouthSong() {
    const audio = new Audio('/src/assets/Houndmouth - Sedona.mp3');
    audio.volume = 0.7; // Adjust volume as needed
    audio.play().catch(err => console.log('Audio play failed:', err));
  }

  function launchRocket() {
    rocketLaunched = true;
    setTimeout(() => {
      nextSlide();
    }, 2500);
  }

  // Update response slide text when userResponse changes
  $effect(() => {
    if (slides[19]) {
      slides[19].text = `Your gift this year is a ${userResponse}!!!`;
    }
  });

  // Auto-advance from "On this years USA trip..." slide
  $effect(() => {
    if (currentSlide === 23 && slides[currentSlide]?.text === "On this years USA trip...") {
      setTimeout(() => {
        if (currentSlide === 23) { // Double check we're still on the same slide
          nextSlide();
        }
      }, 2000);
    }
  });

  // Autoplay Sedona on Houndmouth slide
  $effect(() => {
    if (currentSlide === 24 && slides[currentSlide]?.text === "We will watch HOUNDMOUTH in New Jersey!") {
      playHoundmouthSong();
    }
  });
</script>

<div class="birthday-container">
  <div class="slide-container" class:transitioning={isTransitioning}>
    {#if slides[currentSlide]}
      <div class="slide" class:fade-out={isTransitioning}>
        
        {#if slides[currentSlide].type === "text"}
          <h1 class="slide-text">{slides[currentSlide].text}</h1>
        
        {:else if slides[currentSlide].type === "image"}
          <h1 class="slide-text">{slides[currentSlide].text}</h1>
          <img src={slides[currentSlide].image} alt="Birthday" class="slide-image" />
        
        {:else if slides[currentSlide].type === "emoji"}
          <div class="emoji-slide">{slides[currentSlide].text}</div>
        
        {:else if slides[currentSlide].type === "food"}
          <h1 class="slide-text">{slides[currentSlide].text}</h1>
          <div class="food-container">
            {#each foods as food}
              {#if !foodClicked.has(food.name)}
                <button 
                  class="food-item"
                  onclick={() => clickFood(food.name)}
                >
                  {food.emoji}
                </button>
              {/if}
            {/each}
          </div>
          <p class="food-instruction">{slides[currentSlide].instruction}</p>
        
        {:else if slides[currentSlide].type === "massive"}
          <h1 class="massive-text">{slides[currentSlide].text}</h1>
        
        {:else if slides[currentSlide].type === "tiny"}
          <p class="tiny-text">{slides[currentSlide].text}</p>
        
        {:else if slides[currentSlide].type === "gif"}
          <h1 class="slide-text">{slides[currentSlide].text}</h1>
          <img src="https://media.tenor.com/1wZ88hrB5SwAAAAd/subway-surfer.gif" alt="Subway Surfers" class="gif" />
        
        {:else if slides[currentSlide].type === "input"}
          <h1 class="slide-text">{slides[currentSlide].text}</h1>
          <input 
            type="text" 
            bind:value={userResponse}
            onkeydown={handleInput}
            class="response-input"
            placeholder="Type your wish here..."
            autofocus
          />
        
        {:else if slides[currentSlide].type === "response"}
          <h1 class="slide-text">{slides[19].text}</h1>
        
        {:else if slides[currentSlide].type === "drumroll"}
          <h1 class="slide-text">{slides[currentSlide].text}</h1>
          <button class="drumroll-btn" onclick={playDrumroll}>
            🥁 DRUMROLL 🥁
          </button>
        
        {:else if slides[currentSlide].type === "orbit"}
          <h1 class="slide-text">{slides[currentSlide].text}</h1>
          <div class="orbit-container">
            <img src={slides[currentSlide].elenaImage} alt="Elena orbiting" class="elena-orbit" />
          </div>
        
        {:else if slides[currentSlide].type === "rocket"}
          <div class="rocket-container">
            <img src={slides[currentSlide].image} alt="rocket" class="rocket" class:launched={rocketLaunched} onclick={launchRocket} />
          </div>
        
        {:else if slides[currentSlide].type === "cake-eating"}
          <h1 class="slide-text">{slides[currentSlide].text}</h1>
          <div class="cake-container">
            <button class="cake-btn" onclick={eatCake}>
              🍰
            </button>
          </div>
          <p class="cake-counter">Cakes eaten: {cakesEaten}/5</p>
        
        {:else if slides[currentSlide].type === "small"}
          <p class="small-text">{slides[currentSlide].text}</p>
        
        {/if}
      </div>
    {/if}
  </div>

  <!-- Navigation -->
  {#if slides[currentSlide]?.type !== "drumroll" && !(currentSlide === 23 && slides[currentSlide]?.text === "On this years USA trip...")}
    <div class="navigation">
      <button 
        class="nav-btn" 
        onclick={nextSlide}
        disabled={
          (slides[currentSlide]?.type === "food" && foodClicked.size < foods.length) ||
          (slides[currentSlide]?.type === "input" && !userResponse.trim()) ||
          currentSlide >= slides.length - 1
        }
      >
        {currentSlide >= slides.length - 1 ? "🎉" : "→"}
      </button>
    </div>
  {/if}

  <!-- Confetti for Happy Birthday slide -->
  {#if currentSlide === 2 && slides[currentSlide]?.text === "Happy Birthday!"}
    <Confetti 
      x={[-3, 3]}
      y={[0, 5]}
      delay={[0, 500]} 
      duration={5000} 
      amount={400} 
      fallDistance="100vh"
      colorArray={['#ff6b6b', '#4ecdc4', '#45b7d1', '#f9ca24', '#f0932b', '#eb4d4b', '#6c5ce7']}
    />
  {/if}

  <!-- Confetti for HOUNDMOUTH slide -->
  {#if currentSlide === 24 && slides[currentSlide]?.text === "We will watch HOUNDMOUTH in New Jersey!"}
    <Confetti 
      x={[-3, 3]}
      y={[0, 5]}
      delay={[0, 500]} 
      duration={5000} 
      amount={400} 
      fallDistance="100vh"
      colorArray={['#ff6b6b', '#4ecdc4', '#45b7d1', '#f9ca24', '#f0932b', '#eb4d4b', '#6c5ce7']}
    />
  {/if}
</div>

<style>
  .birthday-container {
    height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-between;
    background: #f5f5f5;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
    position: relative;
    overflow: hidden;
    box-sizing: border-box;
    width: 100vw;
  }

  .slide-container {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    min-height: 0;
    position: relative;
  }

  .slide {
    text-align: center;
    background: #f5f5f5;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
    transition: opacity 0.5s ease-in-out;
    overflow: hidden;
  }

  .fade-out {
    opacity: 0;
  }

  .slide-text {
    font-size: clamp(1.2rem, 3.5vw, 2rem);
    color: #333;
    margin: 5px 0;
    text-shadow: none;
    line-height: 1.2;
    max-width: 600px;
    text-align: center;
    padding-left: 30px;
    padding-right: 30px;
  }

  .slide-image {
    max-width: min(400px, 70vw);
    max-height: min(400px, 50vh);
    border-radius: 15px;
    margin: 5px 0;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
  }

  .emoji-slide {
    font-size: clamp(4rem, 12vw, 8rem);
    animation: bounce 2s infinite;
    line-height: 1;
  }

  .massive-text {
    font-size: 10rem;
    color: #333;
    font-weight: bold;
    text-shadow: none;
    animation: pulse 2s infinite;
    line-height: 1;
    max-width: 800px;
    text-align: center;
  }

  .tiny-text {
    font-size: 0.5rem;
    color: #666;
    margin: 5px 0;
    max-width: 400px;
    text-align: center;
  }

  .food-container {
    display: flex;
    gap: 10px;
    margin: 10px 0;
    flex-wrap: wrap;
    justify-content: center;
  }

  .food-item {
    font-size: clamp(1.8rem, 5vw, 3rem);
    background: none;
    border: none;
    cursor: pointer;
    transition: transform 0.3s ease;
    border-radius: 10px;
    padding: 5px;
  }

  .food-item:hover {
    transform: scale(1.2);
  }

  .food-instruction {
    font-size: clamp(0.9rem, 2vw, 1rem);
    color: #666;
    margin-top: 5px;
    max-width: 400px;
    text-align: center;
  }

  .gif {
    max-width: min(300px, 70vw);
    max-height: min(200px, 25vh);
    border-radius: 15px;
    margin: 5px 0;
  }

  .response-input {
    font-size: clamp(0.9rem, 2.5vw, 1.2rem);
    padding: 8px 12px;
    border: 3px solid #666;
    border-radius: 10px;
    margin: 5px 0;
    width: 80%;
    max-width: 350px;
    text-align: center;
    font-family: inherit;
  }

  .input-instruction {
    font-size: clamp(0.7rem, 1.8vw, 0.9rem);
    color: #666;
    margin-top: 3px;
    max-width: 400px;
    text-align: center;
  }

  .drumroll-btn {
    font-size: clamp(1rem, 2.5vw, 1.5rem);
    padding: 10px 20px;
    background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
    color: white;
    border: none;
    border-radius: 15px;
    cursor: pointer;
    font-family: inherit;
    font-weight: bold;
    margin: 5px 0;
    transition: transform 0.3s ease;
  }

  .drumroll-btn:hover {
    transform: scale(1.1);
  }

  .navigation {
    display: flex;
    position: absolute;
    justify-content: center;
    padding: 5px 0;
    flex-shrink: 0;
    bottom: 150px;
  }

  .nav-btn {
    font-size: clamp(1.5rem, 4vw, 2rem);
    background: none;
    color: #666;
    border: none;
    cursor: pointer;
    padding: 5px 10px;
    transition: all 0.3s ease;
    outline: none;
  }

  .nav-btn:focus {
    outline: none;
  }

  .nav-btn:hover:not(:disabled) {
    color: #d63384;
    transform: scale(1.2);
  }

  .nav-btn:disabled {
    opacity: 0.3;
    cursor: not-allowed;
  }

  .orbit-container {
    position: relative;
    width: 100%;
    height: 200px;
    margin: 20px auto;
    overflow: hidden;
  }

  .elena-orbit {
    width: 120px;
    border-radius: 50%;
    position: absolute;
    top: 50%;
    animation: flyAcross 3s linear infinite, zoom 3s ease-in-out infinite alternate;
  }

  @keyframes flyAcross {
    0% {
      left: -100px;
      transform: translateY(-50%) scale(0.8);
    }
    50% {
      transform: translateY(-50%) scale(1.2);
    }
    100% {
      left: calc(100% + 100px);
      transform: translateY(-50%) scale(0.8);
    }
  }

  @keyframes zoom {
    0% {
      transform: translateY(-50%) scale(0.8);
    }
    100% {
      transform: translateY(-50%) scale(1.2);
    }
  }

  @keyframes bounce {
    0%, 20%, 50%, 80%, 100% {
      transform: translateY(0);
    }
    40% {
      transform: translateY(-15px);
    }
    60% {
      transform: translateY(-8px);
    }
  }

  @keyframes pulse {
    0% {
      transform: scale(1);
    }
    50% {
      transform: scale(1.05);
    }
    100% {
      transform: scale(1);
    }
  }

  @media (max-height: 600px) {
    .birthday-container {
      padding: 5px;
    }
    
    .slide {
      padding: 5px;
    }
    
    .slide-text {
      margin: 20px 20px;
      padding-left: 100px;
      padding-right: 100px;
    }
    
    .slide-image {
      max-height: 40vh;
    }
    
    .gif {
      max-height: 20vh;
    }
    
    .massive-text {
      font-size: clamp(2rem, 6vw, 3.5rem);
    }
  }

  .rocket {
    width: 60px;
    height: 60px;
    object-fit: contain;
    transition: all 2s ease-in-out;
    cursor: pointer;
  }

  .rocket.launched {
    transform: translateY(-200vh) rotate(45deg);
    opacity: 0;
  }

  .cake-container {
    margin: 20px 0;
  }

  .cake-btn {
    font-size: clamp(3rem, 8vw, 6rem);
    background: none;
    border: none;
    cursor: pointer;
    transition: transform 0.3s ease;
    animation: cakeWiggle 2s ease-in-out infinite;
  }

  .cake-btn:hover {
    transform: scale(1.2);
  }

  .cake-counter {
    font-size: clamp(1rem, 2.5vw, 1.5rem);
    color: #666;
    margin-top: 10px;
  }

  @keyframes cakeWiggle {
    0%, 100% { transform: rotate(0deg); }
    25% { transform: rotate(-5deg); }
    75% { transform: rotate(5deg); }
  }

  .play-song-btn {
    font-size: clamp(1rem, 2.5vw, 1.5rem);
    padding: 10px 20px;
    background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
    color: white;
    border: none;
    border-radius: 15px;
    cursor: pointer;
    font-family: inherit;
    font-weight: bold;
    margin: 10px 0;
    transition: transform 0.3s ease;
  }

  .play-song-btn:hover {
    transform: scale(1.1);
  }

  .small-text {
    font-size: clamp(0.8rem, 2vw, 1.2rem);
    color: #666;
    margin: 5px 0;
    max-width: 400px;
    text-align: center;
  }
</style>
