<script lang="ts">
  import { onMount } from 'svelte';

  interface AttemptInfo {
    attempt: number | string;
    delay: string;
    formula: string;
  }

  interface ConfigParam {
    name: string;
    default: string;
    description: string;
  }

  interface ClientReconnectTranslations {
    title: string;
    subtitle: string;
    backoffTitle: string;
    attempts: AttemptInfo[];
    configTitle: string;
    configParams: ConfigParam[];
    recoveryTitle: string;
    recoverySteps: string[];
    disableNote: string;
  }

  interface Props {
    translations: ClientReconnectTranslations;
  }

  let { translations }: Props = $props();

  let sectionRef: HTMLElement | null = $state(null);
  let isVisible = $state(false);

  onMount(() => {
    if (!sectionRef) return;

    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            isVisible = true;
          }
        });
      },
      { threshold: 0.1 }
    );

    observer.observe(sectionRef);
    return () => observer.disconnect();
  });

  // Compute max delay value for relative bar widths
  const maxDelay = $derived(() => {
    let max = 1;
    for (const a of translations.attempts) {
      const numericDelay = parseFloat(String(a.delay).replace(/[^0-9.]/g, ''));
      if (numericDelay > max) max = numericDelay;
    }
    return max;
  });

  function getBarWidth(delay: string): number {
    const numericDelay = parseFloat(String(delay).replace(/[^0-9.]/g, ''));
    const max = maxDelay();
    if (max === 0) return 5;
    return Math.max(5, (numericDelay / max) * 100);
  }
</script>

<section
  bind:this={sectionRef}
  id="client-reconnect"
  class="reconnect-section snap-section bg-bg-secondary"
  aria-labelledby="client-reconnect-title"
>
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-orb gradient-orb-1"></div>
    <div class="gradient-orb gradient-orb-2"></div>
    <div class="dot-pattern"></div>
  </div>

  <div class="content-wrapper w-full max-w-6xl mx-auto relative z-10">
    <h2
      id="client-reconnect-title"
      class="section-title text-3xl md:text-5xl font-bold mb-4 text-text-primary text-center"
      class:visible={isVisible}
    >
      {translations.title}
    </h2>

    <p
      class="section-subtitle text-lg text-text-secondary text-center mb-12 md:mb-16 max-w-2xl mx-auto"
      class:visible={isVisible}
    >
      {translations.subtitle}
    </p>

    <div class="reconnect-grid" class:visible={isVisible}>
      <!-- Exponential Backoff Visualization -->
      <div class="backoff-card glass-card">
        <h3 class="card-section-title">{translations.backoffTitle}</h3>
        <div class="backoff-chart">
          {#each translations.attempts as attempt, i}
            <div class="backoff-row" style="--row-delay: {i * 80}ms">
              <div class="backoff-label">
                <span class="attempt-number">#{attempt.attempt}</span>
                <span class="attempt-delay">{attempt.delay}</span>
              </div>
              <div class="backoff-bar-track">
                <div
                  class="backoff-bar"
                  class:animated={isVisible}
                  style="--bar-width: {getBarWidth(attempt.delay)}%; --bar-delay: {200 + i * 120}ms"
                ></div>
              </div>
              <span class="attempt-formula">{attempt.formula}</span>
            </div>
          {/each}
        </div>
      </div>

      <!-- Configuration Parameters -->
      <div class="config-card glass-card">
        <h3 class="card-section-title">{translations.configTitle}</h3>
        <div class="config-list">
          {#each translations.configParams as param}
            <div class="config-row">
              <div class="config-header">
                <span class="config-name">{param.name}</span>
                <span class="config-default">{param.default}</span>
              </div>
              <p class="config-description">{param.description}</p>
            </div>
          {/each}
        </div>
      </div>
    </div>

    <!-- Subscription Recovery Flow -->
    <div class="recovery-container" class:visible={isVisible}>
      <h3 class="recovery-title">{translations.recoveryTitle}</h3>
      <div class="recovery-steps">
        {#each translations.recoverySteps as step, i}
          <div class="recovery-step">
            <div class="recovery-step-number">{i + 1}</div>
            <span class="recovery-step-label">{step}</span>
          </div>
          {#if i < translations.recoverySteps.length - 1}
            <div class="step-connector" aria-hidden="true">
              <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="connector-arrow">
                <path stroke-linecap="round" stroke-linejoin="round" d="M13.5 4.5L21 12m0 0l-7.5 7.5M21 12H3" />
              </svg>
            </div>
          {/if}
        {/each}
      </div>
      <p class="disable-note">{translations.disableNote}</p>
    </div>
  </div>
</section>

<style>
  .reconnect-section {
    position: relative;
    overflow: hidden;
  }

  .background-elements {
    position: absolute;
    inset: 0;
    pointer-events: none;
  }

  .gradient-orb {
    position: absolute;
    border-radius: 50%;
    filter: blur(100px);
    opacity: 0.08;
  }

  .gradient-orb-1 {
    width: 500px;
    height: 500px;
    background: #38bdf8;
    top: -150px;
    left: -150px;
    animation: orbFloat1 20s ease-in-out infinite;
  }

  .gradient-orb-2 {
    width: 400px;
    height: 400px;
    background: var(--color-accent-primary);
    bottom: -100px;
    right: -100px;
    animation: orbFloat2 18s ease-in-out infinite;
  }

  .dot-pattern {
    position: absolute;
    inset: 0;
    background-image: radial-gradient(rgba(56, 189, 248, 0.06) 1px, transparent 1px);
    background-size: 24px 24px;
  }

  @keyframes orbFloat1 {
    0%, 100% { transform: translate(0, 0); }
    50% { transform: translate(50px, 30px); }
  }

  @keyframes orbFloat2 {
    0%, 100% { transform: translate(0, 0); }
    50% { transform: translate(-40px, -30px); }
  }

  /* Section header animations */
  .section-title {
    opacity: 0;
    transform: translateY(30px);
    transition: opacity 0.6s ease-out, transform 0.6s ease-out;
  }

  .section-title.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .section-subtitle {
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out 0.15s, transform 0.6s ease-out 0.15s;
  }

  .section-subtitle.visible {
    opacity: 1;
    transform: translateY(0);
  }

  /* Main grid: backoff + config side by side */
  .reconnect-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.5rem;
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out 0.3s, transform 0.6s ease-out 0.3s;
  }

  .reconnect-grid.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .glass-card {
    background: rgba(26, 26, 46, 0.6);
    border: 1px solid var(--color-border);
    border-radius: 16px;
    backdrop-filter: blur(12px);
    padding: 1.5rem;
    min-width: 0;
    overflow: hidden;
  }

  .card-section-title {
    font-size: 1.1rem;
    font-weight: 600;
    color: #38bdf8;
    margin-bottom: 1.25rem;
  }

  /* ========== Backoff Chart ========== */
  .backoff-chart {
    display: flex;
    flex-direction: column;
    gap: 0.75rem;
  }

  .backoff-row {
    display: grid;
    grid-template-columns: 5.5rem 1fr auto;
    align-items: center;
    gap: 0.75rem;
  }

  .backoff-label {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    min-width: 0;
  }

  .attempt-number {
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: 0.75rem;
    font-weight: 600;
    color: #38bdf8;
    white-space: nowrap;
  }

  .attempt-delay {
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: 0.7rem;
    color: var(--color-text-secondary);
    white-space: nowrap;
  }

  .backoff-bar-track {
    height: 20px;
    background: rgba(56, 189, 248, 0.05);
    border-radius: 4px;
    overflow: hidden;
    min-width: 0;
  }

  .backoff-bar {
    height: 100%;
    width: 0%;
    background: linear-gradient(90deg, rgba(56, 189, 248, 0.3), #38bdf8);
    border-radius: 4px;
    transition: width 0.8s cubic-bezier(0.22, 1, 0.36, 1);
    transition-delay: var(--bar-delay, 0ms);
  }

  .backoff-bar.animated {
    width: var(--bar-width, 0%);
  }

  .attempt-formula {
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: 0.65rem;
    color: var(--color-text-secondary);
    opacity: 0.7;
    white-space: nowrap;
    text-align: right;
    min-width: 4rem;
  }

  /* ========== Config Parameters ========== */
  .config-list {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }

  .config-row {
    padding: 0.75rem;
    background: rgba(18, 18, 26, 0.5);
    border: 1px solid rgba(255, 255, 255, 0.04);
    border-radius: 10px;
  }

  .config-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 0.75rem;
    margin-bottom: 0.375rem;
  }

  .config-name {
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: 0.8rem;
    font-weight: 600;
    color: #38bdf8;
  }

  .config-default {
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: 0.75rem;
    color: var(--color-accent-primary);
    padding: 0.125rem 0.5rem;
    background: rgba(0, 255, 136, 0.08);
    border-radius: 4px;
    white-space: nowrap;
  }

  .config-description {
    font-size: 0.8rem;
    color: var(--color-text-secondary);
    line-height: 1.5;
    margin: 0;
  }

  /* ========== Recovery Steps ========== */
  .recovery-container {
    margin-top: 2rem;
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out 0.5s, transform 0.6s ease-out 0.5s;
  }

  .recovery-container.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .recovery-title {
    font-size: 1.1rem;
    font-weight: 600;
    color: var(--color-text-primary);
    text-align: center;
    margin-bottom: 1.25rem;
  }

  .recovery-steps {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
    gap: 0.375rem;
  }

  .recovery-step {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.5rem 0.875rem;
    background: rgba(26, 26, 46, 0.6);
    border: 1px solid var(--color-border);
    border-radius: 8px;
    transition: border-color 0.2s ease;
  }

  .recovery-step:hover {
    border-color: rgba(56, 189, 248, 0.4);
  }

  .recovery-step-number {
    width: 22px;
    height: 22px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;
    background: rgba(56, 189, 248, 0.15);
    color: #38bdf8;
    font-size: 0.7rem;
    font-weight: 600;
    flex-shrink: 0;
  }

  .recovery-step-label {
    font-size: 0.8rem;
    color: var(--color-text-secondary);
    white-space: nowrap;
  }

  .step-connector {
    display: flex;
    align-items: center;
  }

  .connector-arrow {
    width: 16px;
    height: 16px;
    color: rgba(56, 189, 248, 0.4);
  }

  .disable-note {
    margin-top: 1rem;
    text-align: center;
    font-size: 0.78rem;
    color: var(--color-text-secondary);
    opacity: 0.75;
    padding: 0.5rem 1rem;
    background: rgba(56, 189, 248, 0.04);
    border: 1px solid rgba(56, 189, 248, 0.1);
    border-radius: 8px;
    max-width: 36rem;
    margin-left: auto;
    margin-right: auto;
  }

  /* ========== Responsive ========== */
  @media (max-width: 1024px) {
    .reconnect-grid {
      grid-template-columns: 1fr;
    }
  }

  @media (max-width: 768px) {
    .glass-card {
      padding: 1rem;
    }

    .card-section-title {
      font-size: 1rem;
      margin-bottom: 1rem;
    }

    .backoff-row {
      grid-template-columns: 4.5rem 1fr;
      gap: 0.5rem;
    }

    .attempt-formula {
      display: none;
    }

    .attempt-number {
      font-size: 0.7rem;
    }

    .attempt-delay {
      font-size: 0.65rem;
    }

    .backoff-bar-track {
      height: 16px;
    }

    .config-row {
      padding: 0.625rem;
    }

    .config-name {
      font-size: 0.75rem;
    }

    .config-default {
      font-size: 0.7rem;
    }

    .config-description {
      font-size: 0.75rem;
    }

    .recovery-steps {
      flex-direction: column;
      align-items: stretch;
    }

    .step-connector {
      transform: rotate(90deg);
      align-self: center;
    }

    .recovery-step-label {
      white-space: normal;
      font-size: 0.75rem;
    }

    .recovery-step {
      padding: 0.5rem 0.75rem;
    }

    .gradient-orb-1,
    .gradient-orb-2 {
      opacity: 0.05;
    }
  }

  /* ========== Reduced Motion ========== */
  @media (prefers-reduced-motion: reduce) {
    .section-title,
    .section-subtitle,
    .reconnect-grid,
    .recovery-container {
      transition: none;
      opacity: 1;
      transform: none;
    }

    .backoff-bar {
      transition: none;
    }

    .backoff-bar.animated {
      width: var(--bar-width, 0%);
    }

    .gradient-orb-1,
    .gradient-orb-2 {
      animation: none;
    }
  }
</style>
