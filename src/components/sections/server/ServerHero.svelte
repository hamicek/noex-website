<script lang="ts">
  import { onMount } from 'svelte';

  interface ServerHeroTranslations {
    headline: string;
    subheadline: string;
    install: string;
    github: string;
  }

  interface Props {
    translations: ServerHeroTranslations;
    githubUrl?: string;
  }

  let {
    translations,
    githubUrl = 'https://github.com/hamicek/noex-server',
  }: Props = $props();

  let isVisible = $state(false);
  let copied = $state(false);

  const installCommand = 'npm install @hamicek/noex-server';

  onMount(() => {
    requestAnimationFrame(() => {
      isVisible = true;
    });
  });

  async function copyInstallCommand() {
    try {
      await navigator.clipboard.writeText(installCommand);
      copied = true;
      setTimeout(() => { copied = false; }, 2000);
    } catch {
      const textarea = document.createElement('textarea');
      textarea.value = installCommand;
      document.body.appendChild(textarea);
      textarea.select();
      document.execCommand('copy');
      document.body.removeChild(textarea);
      copied = true;
      setTimeout(() => { copied = false; }, 2000);
    }
  }

  function scrollToFeatures() {
    const section = document.getElementById('server-features');
    if (section) {
      section.scrollIntoView({ behavior: 'smooth' });
    }
  }
</script>

<section id="server-hero" class="hero-section snap-section bg-bg-primary">
  <!-- Animated Grid Background -->
  <div class="grid-background" aria-hidden="true">
    <div class="grid-overlay"></div>
    <div class="glow-orb glow-orb-1"></div>
    <div class="glow-orb glow-orb-2"></div>
  </div>

  <!-- Floating Code Background -->
  <div class="code-background" aria-hidden="true">
    <pre class="floating-code"><code><span class="code-keyword">const</span> server = <span class="code-keyword">await</span> <span class="code-class">NoexServer</span>.<span class="code-method">start</span>({'{'}</code>
<code>  <span class="code-property">store</span>,</code>
<code>  <span class="code-property">rules</span>,</code>
<code>  <span class="code-property">port</span>: <span class="code-number">8080</span>,</code>
<code>  <span class="code-property">auth</span>: {'{'} <span class="code-property">validate</span>: verifyToken {'}'},</code>
<code>{'}'});</code></pre>
  </div>

  <!-- Main Content -->
  <div class="hero-content max-w-4xl mx-auto text-center relative z-10" class:visible={isVisible}>
    <!-- Package Badge -->
    <div class="package-badge">
      <span class="badge-icon" aria-hidden="true">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
          <path stroke-linecap="round" stroke-linejoin="round" d="M5.25 14.25h13.5m-13.5 0a3 3 0 01-3-3m3 3a3 3 0 100 6h13.5a3 3 0 100-6m-16.5-3a3 3 0 013-3h13.5a3 3 0 013 3m-19.5 0a4.5 4.5 0 01.9-2.7L5.737 5.1a3.375 3.375 0 012.7-1.35h7.126c1.062 0 2.062.5 2.7 1.35l2.587 3.45a4.5 4.5 0 01.9 2.7m0 0a3 3 0 01-3 3m0 3h.008v.008h-.008v-.008zm0-6h.008v.008h-.008v-.008zm-3 6h.008v.008h-.008v-.008zm0-6h.008v.008h-.008v-.008z" />
        </svg>
      </span>
      <span class="badge-text">noex ecosystem</span>
    </div>

    <!-- Logo -->
    <h1 class="text-5xl md:text-7xl font-bold mb-6">
      <span class="logo-text">noex-server</span>
    </h1>

    <!-- Headline -->
    <p class="headline text-2xl md:text-4xl font-semibold mb-4 text-text-primary">
      {translations.headline}
    </p>

    <!-- Subheadline -->
    <p class="subheadline text-lg md:text-xl text-text-secondary mb-8 max-w-3xl mx-auto">
      {translations.subheadline}
    </p>

    <!-- Install Command -->
    <div class="install-bar">
      <button class="install-button" onclick={copyInstallCommand}>
        <span class="install-prompt" aria-hidden="true">$</span>
        <code class="install-text">{translations.install}</code>
        <span class="install-copy">
          {#if copied}
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="copy-check">
              <path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" />
            </svg>
          {:else}
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" class="copy-icon">
              <path stroke-linecap="round" stroke-linejoin="round" d="M15.666 3.888A2.25 2.25 0 0013.5 2.25h-3c-1.03 0-1.9.693-2.166 1.638m7.332 0c.055.194.084.4.084.612v0a.75.75 0 01-.75.75H9a.75.75 0 01-.75-.75v0c0-.212.03-.418.084-.612m7.332 0c.646.049 1.288.11 1.927.184 1.1.128 1.907 1.077 1.907 2.185V19.5a2.25 2.25 0 01-2.25 2.25H6.75A2.25 2.25 0 014.5 19.5V6.257c0-1.108.806-2.057 1.907-2.185a48.208 48.208 0 011.927-.184" />
            </svg>
          {/if}
        </span>
      </button>
    </div>

    <!-- CTA Buttons -->
    <div class="cta-buttons flex flex-col sm:flex-row gap-4 justify-center mt-6">
      <a
        href={githubUrl}
        target="_blank"
        rel="noopener noreferrer"
        class="cta-secondary px-8 py-3 border border-border text-text-primary rounded-lg hover:border-accent-server hover:text-accent-server transition-all"
      >
        <svg viewBox="0 0 24 24" fill="currentColor" class="cta-icon" aria-hidden="true">
          <path d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"/>
        </svg>
        {translations.github}
      </a>
    </div>
  </div>

  <!-- Scroll Down Indicator -->
  <button
    type="button"
    class="scroll-indicator absolute bottom-8 left-1/2 -translate-x-1/2"
    onclick={scrollToFeatures}
    aria-label="Scroll to features"
  >
    <svg
      class="w-6 h-6 text-text-secondary"
      fill="none"
      stroke="currentColor"
      viewBox="0 0 24 24"
      aria-hidden="true"
    >
      <path
        stroke-linecap="round"
        stroke-linejoin="round"
        stroke-width="2"
        d="M19 14l-7 7m0 0l-7-7m7 7V3"
      />
    </svg>
  </button>
</section>

<style>
  .hero-section {
    position: relative;
    overflow: hidden;
  }

  .grid-background {
    position: absolute;
    inset: 0;
    overflow: hidden;
    pointer-events: none;
  }

  .grid-overlay {
    position: absolute;
    inset: 0;
    background-image:
      linear-gradient(rgba(167, 139, 250, 0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(167, 139, 250, 0.03) 1px, transparent 1px);
    background-size: 60px 60px;
    animation: gridPulse 4s ease-in-out infinite;
  }

  @keyframes gridPulse {
    0%, 100% { opacity: 0.5; }
    50% { opacity: 1; }
  }

  .glow-orb {
    position: absolute;
    border-radius: 50%;
    filter: blur(80px);
    opacity: 0.35;
  }

  .glow-orb-1 {
    width: 400px;
    height: 400px;
    background: #a78bfa;
    top: -100px;
    right: -100px;
    animation: orbFloat1 8s ease-in-out infinite;
  }

  .glow-orb-2 {
    width: 300px;
    height: 300px;
    background: var(--color-accent-primary);
    bottom: -50px;
    left: -50px;
    animation: orbFloat2 10s ease-in-out infinite;
  }

  @keyframes orbFloat1 {
    0%, 100% { transform: translate(0, 0) scale(1); }
    50% { transform: translate(-30px, 30px) scale(1.1); }
  }

  @keyframes orbFloat2 {
    0%, 100% { transform: translate(0, 0) scale(1); }
    50% { transform: translate(20px, -20px) scale(1.05); }
  }

  .code-background {
    position: absolute;
    inset: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    pointer-events: none;
    opacity: 0.05;
  }

  .floating-code {
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: clamp(0.7rem, 2vw, 1.2rem);
    line-height: 1.8;
    white-space: pre;
    animation: codeFloat 6s ease-in-out infinite;
  }

  .floating-code code {
    display: block;
  }

  @keyframes codeFloat {
    0%, 100% { transform: translateY(0) rotate(-2deg); }
    50% { transform: translateY(-10px) rotate(2deg); }
  }

  .code-keyword { color: #a78bfa; }
  .code-class { color: var(--color-accent-primary); }
  .code-method { color: #dcdcaa; }
  .code-string { color: #ce9178; }
  .code-property { color: #9cdcfe; }
  .code-number { color: #b5cea8; }

  .package-badge {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.375rem 1rem;
    background: rgba(167, 139, 250, 0.1);
    border: 1px solid rgba(167, 139, 250, 0.3);
    border-radius: 2rem;
    margin-bottom: 1.5rem;
    opacity: 0;
    transform: translateY(10px);
    transition: opacity 0.5s ease-out, transform 0.5s ease-out;
  }

  .hero-content.visible .package-badge {
    opacity: 1;
    transform: translateY(0);
  }

  .badge-icon {
    width: 18px;
    height: 18px;
    color: #a78bfa;
  }

  .badge-icon svg {
    width: 100%;
    height: 100%;
  }

  .badge-text {
    font-size: 0.8rem;
    font-weight: 500;
    color: #a78bfa;
  }

  .logo-text {
    color: #a78bfa;
    text-shadow: 0 0 40px rgba(167, 139, 250, 0.5);
  }

  .hero-content {
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.8s ease-out, transform 0.8s ease-out;
  }

  .hero-content.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .install-bar {
    display: flex;
    justify-content: center;
  }

  .install-button {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 14px 20px;
    background: rgba(18, 18, 26, 0.8);
    border: 1px solid var(--color-border);
    border-radius: 10px;
    cursor: pointer;
    transition: all 0.2s ease;
  }

  .install-button:hover {
    border-color: #a78bfa;
    box-shadow: 0 0 20px rgba(167, 139, 250, 0.2);
  }

  .install-button:focus-visible {
    outline: 2px solid #a78bfa;
    outline-offset: 2px;
  }

  .install-prompt {
    color: var(--color-text-secondary);
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
  }

  .install-text {
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: 1rem;
    color: #a78bfa;
  }

  .install-copy {
    width: 20px;
    height: 20px;
    color: var(--color-text-secondary);
    transition: color 0.2s ease;
  }

  .install-copy svg {
    width: 100%;
    height: 100%;
  }

  .install-button:hover .install-copy {
    color: #a78bfa;
  }

  .copy-check {
    color: var(--color-accent-primary);
  }

  .cta-icon {
    width: 18px;
    height: 18px;
  }

  .cta-secondary {
    display: inline-flex;
    align-items: center;
    gap: 8px;
  }

  .cta-secondary:hover {
    transform: translateY(-2px);
    border-color: #a78bfa !important;
    color: #a78bfa !important;
    box-shadow: 0 0 20px rgba(167, 139, 250, 0.2);
  }

  .scroll-indicator {
    background: transparent;
    border: none;
    cursor: pointer;
    padding: 0.5rem;
    animation: bounce 2s infinite;
    transition: color 0.2s ease;
  }

  .scroll-indicator:hover {
    color: #a78bfa;
  }

  .scroll-indicator:focus-visible {
    outline: 2px solid #a78bfa;
    outline-offset: 2px;
    border-radius: 4px;
  }

  @keyframes bounce {
    0%, 20%, 50%, 80%, 100% { transform: translateX(-50%) translateY(0); }
    40% { transform: translateX(-50%) translateY(-10px); }
    60% { transform: translateX(-50%) translateY(-5px); }
  }

  @media (max-width: 768px) {
    .glow-orb-1 { width: 250px; height: 250px; }
    .glow-orb-2 { width: 200px; height: 200px; }
    .code-background { opacity: 0.03; }
    .install-text { font-size: 0.85rem; }
  }

  @media (prefers-reduced-motion: reduce) {
    .grid-overlay,
    .glow-orb,
    .floating-code,
    .scroll-indicator {
      animation: none;
    }
    .hero-content {
      transition: none;
      opacity: 1;
      transform: none;
    }
    .package-badge {
      transition: none;
      opacity: 1;
      transform: none;
    }
    .cta-secondary:hover {
      transform: none;
    }
  }
</style>
