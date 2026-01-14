<script lang="ts">
  import { page } from '$app/state';

  let { status = page.status } = $props();
</script>

<svelte:head>
  <title>{status} - SYSTEM ERROR</title>
</svelte:head>

<main class="error-container">
  <div class="bg-glow"></div>
  <div class="anime-card error-card">
    <h1 class="glitch-text" data-text={status}>{status}</h1>
    <div class="error-msg">
      {status === 404
        ? 'SYSTEM ERROR: PAGE_NOT_FOUND'
        : 'SYSTEM ERROR: UNKNOWN_FAILURE'}
    </div>
    <p class="error-description">
      你所尋找的頁面似乎已進入虛擬空隙，或是它的代碼已被抹除。
    </p>
    <a href="/" class="home-btn">
      <span class="btn-text">返回主節點</span>
      <span class="btn-glitch"></span>
    </a>
  </div>
</main>

<style>
  .error-container {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 80vh;
    text-align: center;
  }

  .error-card {
    max-width: 500px;
    width: 90%;
  }

  .glitch-text {
    font-size: 8rem;
    margin: 0;
    line-height: 1;
    color: var(--accent-color);
    position: relative;
    text-shadow:
      0.05em 0 0 #ff00ffbf,
      -0.025em -0.05em 0 #00ff00bf,
      0.025em 0.05em 0 #0000ffbf;
    animation: glitch 500ms infinite;
  }

  .error-msg {
    font-family: 'Courier New', Courier, monospace;
    color: var(--secondary-color);
    font-weight: bold;
    letter-spacing: 2px;
    margin-bottom: 1.5rem;
    opacity: 0.8;
  }

  .error-description {
    color: var(--text-color);
    margin-bottom: 2.5rem;
    line-height: 1.8;
  }

  .home-btn {
    display: inline-block;
    padding: 1rem 2.5rem;
    background: transparent;
    border: 1px solid var(--accent-color);
    color: white;
    text-decoration: none;
    font-weight: bold;
    position: relative;
    overflow: hidden;
    transition: all 0.3s;
    border-radius: 4px;
  }

  .home-btn:hover {
    background: var(--accent-color);
    color: #000;
    box-shadow: 0 0 20px var(--accent-color);
  }

  .bg-glow {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 300px;
    height: 300px;
    background: var(--accent-color);
    filter: blur(150px);
    opacity: 0.1;
    z-index: -1;
  }

  @keyframes glitch {
    0% {
      text-shadow:
        0.05em 0 0 #ff00ffbf,
        -0.05em -0.025em 0 #00ffffbf,
        -0.025em 0.05em 0 #ffff00bf;
    }
    14% {
      text-shadow:
        0.05em 0 0 #ff00ffbf,
        -0.05em -0.025em 0 #00ffffbf,
        -0.025em 0.05em 0 #ffff00bf;
    }
    15% {
      text-shadow:
        -0.05em -0.025em 0 #ff00ffbf,
        0.025em 0.025em 0 #00ffffbf,
        -0.05em -0.05em 0 #ffff00bf;
    }
    49% {
      text-shadow:
        -0.05em -0.025em 0 #ff00ffbf,
        0.025em 0.025em 0 #00ffffbf,
        -0.05em -0.05em 0 #ffff00bf;
    }
    50% {
      text-shadow:
        0.025em 0.05em 0 #ff00ffbf,
        0.05em 0 0 #00ffffbf,
        0 -0.05em 0 #ffff00bf;
    }
    99% {
      text-shadow:
        0.025em 0.05em 0 #ff00ffbf,
        0.05em 0 0 #00ffffbf,
        0 -0.05em 0 #ffff00bf;
    }
    100% {
      text-shadow:
        -0.025em 0 0 #ff00ffbf,
        -0.025em -0.025em 0 #00ffffbf,
        -0.025em -0.05em 0 #ffff00bf;
    }
  }

  @media (max-width: 600px) {
    .glitch-text {
      font-size: 5rem;
    }
  }
</style>
