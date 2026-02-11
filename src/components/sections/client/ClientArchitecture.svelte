<script lang="ts">
  import { onMount } from 'svelte';

  interface ClientArchitectureTranslations {
    title: string;
    subtitle: string;
    layers: {
      api: string;
      apiDesc: string;
      subscription: string;
      subscriptionDesc: string;
      protocol: string;
      protocolDesc: string;
      transport: string;
      transportDesc: string;
    };
    stateMachine: string;
    states: {
      disconnected: string;
      connecting: string;
      connected: string;
      reconnecting: string;
    };
    lifecycleEvents: string;
    requestPipeline: string;
  }

  interface Props {
    translations: ClientArchitectureTranslations;
  }

  let { translations }: Props = $props();

  let sectionRef: HTMLElement | null = $state(null);
  let isVisible = $state(false);
  let layersVisible = $state(false);
  let stateMachineVisible = $state(false);
  let infoVisible = $state(false);

  onMount(() => {
    if (!sectionRef) return;

    const prefersReduced = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            isVisible = true;
            if (prefersReduced) {
              layersVisible = true;
              stateMachineVisible = true;
              infoVisible = true;
            } else {
              setTimeout(() => { layersVisible = true; }, 300);
              setTimeout(() => { stateMachineVisible = true; }, 700);
              setTimeout(() => { infoVisible = true; }, 1100);
            }
          }
        });
      },
      { threshold: 0.15 }
    );

    observer.observe(sectionRef);
    return () => observer.disconnect();
  });
</script>

<section
  bind:this={sectionRef}
  id="client-architecture"
  class="architecture-section snap-section bg-bg-primary"
  aria-labelledby="client-architecture-title"
>
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-orb gradient-orb-1"></div>
    <div class="gradient-orb gradient-orb-2"></div>
    <div class="dot-pattern"></div>
  </div>

  <div class="content-wrapper max-w-6xl mx-auto relative z-10">
    <!-- Title + Subtitle -->
    <h2
      id="client-architecture-title"
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

    <!-- Main content: Layers + State Machine side by side on desktop -->
    <div class="architecture-grid">
      <!-- Layered Diagram -->
      <div class="layers-diagram" class:visible={layersVisible}>
        <!-- API Layer -->
        <div class="layer-box layer-blue">
          <div class="layer-header">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" class="layer-icon" aria-hidden="true">
              <path stroke-linecap="round" stroke-linejoin="round" d="M14.25 9.75L16.5 12l-2.25 2.25m-4.5 0L7.5 12l2.25-2.25M6 20.25h12A2.25 2.25 0 0020.25 18V6A2.25 2.25 0 0018 3.75H6A2.25 2.25 0 003.75 6v12A2.25 2.25 0 006 20.25z" />
            </svg>
            <span class="layer-name">{translations.layers.api}</span>
          </div>
          <p class="layer-desc">{translations.layers.apiDesc}</p>
        </div>

        <!-- Connector -->
        <div class="layer-connector" aria-hidden="true">
          <div class="connector-line-vertical"></div>
          <div class="connector-arrow"></div>
        </div>

        <!-- Subscription Manager -->
        <div class="layer-box layer-green">
          <div class="layer-header">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" class="layer-icon" aria-hidden="true">
              <path stroke-linecap="round" stroke-linejoin="round" d="M14.857 17.082a23.848 23.848 0 005.454-1.31A8.967 8.967 0 0118 9.75v-.7V9A6 6 0 006 9v.75a8.967 8.967 0 01-2.312 6.022c1.733.64 3.56 1.085 5.455 1.31m5.714 0a24.255 24.255 0 01-5.714 0m5.714 0a3 3 0 11-5.714 0" />
            </svg>
            <span class="layer-name">{translations.layers.subscription}</span>
          </div>
          <p class="layer-desc">{translations.layers.subscriptionDesc}</p>
        </div>

        <!-- Connector -->
        <div class="layer-connector" aria-hidden="true">
          <div class="connector-line-vertical"></div>
          <div class="connector-arrow"></div>
        </div>

        <!-- Protocol Layer -->
        <div class="layer-box layer-blue">
          <div class="layer-header">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" class="layer-icon" aria-hidden="true">
              <path stroke-linecap="round" stroke-linejoin="round" d="M7.5 21L3 16.5m0 0L7.5 12M3 16.5h13.5m0-13.5L21 7.5m0 0L16.5 12M21 7.5H7.5" />
            </svg>
            <span class="layer-name">{translations.layers.protocol}</span>
          </div>
          <p class="layer-desc">{translations.layers.protocolDesc}</p>
        </div>

        <!-- Connector -->
        <div class="layer-connector" aria-hidden="true">
          <div class="connector-line-vertical"></div>
          <div class="connector-arrow"></div>
        </div>

        <!-- Transport Layer -->
        <div class="layer-box layer-green">
          <div class="layer-header">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" class="layer-icon" aria-hidden="true">
              <path stroke-linecap="round" stroke-linejoin="round" d="M8.288 15.038a5.25 5.25 0 017.424 0M5.106 11.856c3.807-3.808 9.98-3.808 13.788 0M1.924 8.674c5.565-5.565 14.587-5.565 20.152 0M12.53 18.22l-.53.53-.53-.53a.75.75 0 011.06 0z" />
            </svg>
            <span class="layer-name">{translations.layers.transport}</span>
          </div>
          <p class="layer-desc">{translations.layers.transportDesc}</p>
        </div>
      </div>

      <!-- State Machine Diagram -->
      <div class="state-machine" class:visible={stateMachineVisible}>
        <h3 class="state-machine-title">{translations.stateMachine}</h3>

        <div class="state-diagram" role="img" aria-label="Connection state machine: disconnected, connecting, connected, reconnecting">
          <!-- States arranged in a 2x2 grid with arrows -->
          <div class="states-grid">
            <!-- Disconnected -->
            <div class="state-bubble state-disconnected">
              <div class="state-dot disconnected-dot" aria-hidden="true"></div>
              <span>{translations.states.disconnected}</span>
            </div>

            <!-- Connecting -->
            <div class="state-bubble state-connecting">
              <div class="state-dot connecting-dot" aria-hidden="true"></div>
              <span>{translations.states.connecting}</span>
            </div>

            <!-- Reconnecting -->
            <div class="state-bubble state-reconnecting">
              <div class="state-dot reconnecting-dot" aria-hidden="true"></div>
              <span>{translations.states.reconnecting}</span>
            </div>

            <!-- Connected -->
            <div class="state-bubble state-connected">
              <div class="state-dot connected-dot" aria-hidden="true"></div>
              <span>{translations.states.connected}</span>
            </div>
          </div>

          <!-- Arrow overlay using SVG -->
          <svg class="arrows-svg" viewBox="0 0 320 240" fill="none" aria-hidden="true">
            <!-- disconnected -> connecting (top-left to top-right) -->
            <line x1="115" y1="50" x2="185" y2="50" stroke="rgba(56, 189, 248, 0.5)" stroke-width="2" />
            <polygon points="185,45 195,50 185,55" fill="rgba(56, 189, 248, 0.5)" />

            <!-- connecting -> connected (top-right to bottom-right) -->
            <line x1="250" y1="80" x2="250" y2="150" stroke="rgba(0, 255, 136, 0.5)" stroke-width="2" />
            <polygon points="245,150 250,160 255,150" fill="rgba(0, 255, 136, 0.5)" />

            <!-- connected -> reconnecting (bottom-right to bottom-left) -->
            <line x1="185" y1="190" x2="115" y2="190" stroke="rgba(251, 191, 36, 0.5)" stroke-width="2" />
            <polygon points="115,185 105,190 115,195" fill="rgba(251, 191, 36, 0.5)" />

            <!-- reconnecting -> connecting (bottom-left to top-right, curved) -->
            <path d="M 70 160 C 40 120, 140 80, 200 80" stroke="rgba(56, 189, 248, 0.35)" stroke-width="1.5" stroke-dasharray="4 3" />
            <polygon points="197,75 207,80 197,85" fill="rgba(56, 189, 248, 0.35)" />

            <!-- reconnecting -> disconnected (bottom-left to top-left) -->
            <line x1="70" y1="160" x2="70" y2="80" stroke="rgba(248, 113, 113, 0.4)" stroke-width="1.5" stroke-dasharray="4 3" />
            <polygon points="65,80 70,70 75,80" fill="rgba(248, 113, 113, 0.4)" />
          </svg>
        </div>
      </div>
    </div>

    <!-- Info Cards -->
    <div class="info-cards grid md:grid-cols-2 gap-6 mt-10 md:mt-14" class:visible={infoVisible}>
      <div class="info-card glass-card">
        <div class="info-icon accent-sky" aria-hidden="true">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
            <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 13.5l10.5-11.25L12 10.5h8.25L9.75 21.75 12 13.5H3.75z" />
          </svg>
        </div>
        <p class="info-text">{translations.lifecycleEvents}</p>
      </div>
      <div class="info-card glass-card">
        <div class="info-icon accent-green" aria-hidden="true">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
            <path stroke-linecap="round" stroke-linejoin="round" d="M19.5 12c0-1.232-.046-2.453-.138-3.662a4.006 4.006 0 00-3.7-3.7 48.678 48.678 0 00-7.324 0 4.006 4.006 0 00-3.7 3.7c-.017.22-.032.441-.046.662M19.5 12l3-3m-3 3l-3-3m-12 3c0 1.232.046 2.453.138 3.662a4.006 4.006 0 003.7 3.7 48.656 48.656 0 007.324 0 4.006 4.006 0 003.7-3.7c.017-.22.032-.441.046-.662M4.5 12l3 3m-3-3l-3 3" />
          </svg>
        </div>
        <p class="info-text">{translations.requestPipeline}</p>
      </div>
    </div>
  </div>
</section>

<style>
  .architecture-section {
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
    background: #38bdf8;
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

  /* Title / Subtitle animations */
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

  /* Architecture Grid: layers left, state machine right */
  .architecture-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 3rem;
    align-items: start;
  }

  /* Layers Diagram */
  .layers-diagram {
    display: flex;
    flex-direction: column;
    align-items: stretch;
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out, transform 0.6s ease-out;
  }

  .layers-diagram.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .layer-box {
    padding: 1rem 1.25rem;
    border-radius: 10px;
    background: rgba(26, 26, 46, 0.7);
    border: 1px solid var(--color-border);
    backdrop-filter: blur(8px);
    transition: border-color 0.3s ease, box-shadow 0.3s ease, transform 0.3s ease;
  }

  .layer-box:hover {
    transform: translateY(-2px);
  }

  .layer-box.layer-blue {
    border-color: rgba(56, 189, 248, 0.25);
  }

  .layer-box.layer-blue:hover {
    border-color: rgba(56, 189, 248, 0.5);
    box-shadow: 0 0 20px rgba(56, 189, 248, 0.1);
  }

  .layer-box.layer-green {
    border-color: rgba(0, 255, 136, 0.25);
  }

  .layer-box.layer-green:hover {
    border-color: rgba(0, 255, 136, 0.5);
    box-shadow: 0 0 20px rgba(0, 255, 136, 0.1);
  }

  .layer-header {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    margin-bottom: 0.35rem;
  }

  .layer-icon {
    width: 18px;
    height: 18px;
    flex-shrink: 0;
  }

  .layer-blue .layer-icon {
    color: #38bdf8;
  }

  .layer-green .layer-icon {
    color: var(--color-accent-primary);
  }

  .layer-name {
    font-weight: 600;
    font-size: 0.95rem;
    color: var(--color-text-primary);
  }

  .layer-blue .layer-name {
    color: #7dd3fc;
  }

  .layer-green .layer-name {
    color: var(--color-accent-primary);
  }

  .layer-desc {
    font-size: 0.8rem;
    color: var(--color-text-secondary);
    line-height: 1.5;
    margin: 0;
    padding-left: 1.75rem;
  }

  /* Layer connectors */
  .layer-connector {
    display: flex;
    flex-direction: column;
    align-items: center;
    height: 24px;
    position: relative;
  }

  .connector-line-vertical {
    width: 2px;
    flex: 1;
    background: linear-gradient(to bottom, rgba(56, 189, 248, 0.3), rgba(0, 255, 136, 0.3));
  }

  .connector-arrow {
    width: 0;
    height: 0;
    border-left: 5px solid transparent;
    border-right: 5px solid transparent;
    border-top: 6px solid rgba(56, 189, 248, 0.4);
  }

  /* State Machine */
  .state-machine {
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out, transform 0.6s ease-out;
  }

  .state-machine.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .state-machine-title {
    font-size: 1rem;
    font-weight: 600;
    color: var(--color-text-secondary);
    text-align: center;
    margin-bottom: 1.25rem;
    letter-spacing: 0.02em;
  }

  .state-diagram {
    position: relative;
    background: rgba(26, 26, 46, 0.5);
    border: 1px solid var(--color-border);
    border-radius: 14px;
    backdrop-filter: blur(8px);
    padding: 2rem 1.5rem;
  }

  .states-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 3.5rem 2rem;
    position: relative;
    z-index: 1;
  }

  .state-bubble {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.6rem 1rem;
    border-radius: 20px;
    background: rgba(26, 26, 46, 0.9);
    border: 1px solid var(--color-border);
    font-size: 0.8rem;
    font-weight: 500;
    color: var(--color-text-primary);
    transition: border-color 0.3s ease, box-shadow 0.3s ease;
    justify-content: center;
    white-space: nowrap;
  }

  .state-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    flex-shrink: 0;
  }

  .disconnected-dot {
    background: #6b7280;
    box-shadow: 0 0 6px rgba(107, 114, 128, 0.4);
  }

  .connecting-dot {
    background: #38bdf8;
    box-shadow: 0 0 6px rgba(56, 189, 248, 0.4);
    animation: pulse-dot 2s ease-in-out infinite;
  }

  .connected-dot {
    background: #00ff88;
    box-shadow: 0 0 6px rgba(0, 255, 136, 0.4);
  }

  .reconnecting-dot {
    background: #fbbf24;
    box-shadow: 0 0 6px rgba(251, 191, 36, 0.4);
    animation: pulse-dot 1.5s ease-in-out infinite;
  }

  @keyframes pulse-dot {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.4; }
  }

  .state-disconnected {
    border-color: rgba(107, 114, 128, 0.3);
  }

  .state-disconnected:hover {
    border-color: rgba(107, 114, 128, 0.6);
    box-shadow: 0 0 15px rgba(107, 114, 128, 0.1);
  }

  .state-connecting {
    border-color: rgba(56, 189, 248, 0.3);
  }

  .state-connecting:hover {
    border-color: rgba(56, 189, 248, 0.6);
    box-shadow: 0 0 15px rgba(56, 189, 248, 0.1);
  }

  .state-connected {
    border-color: rgba(0, 255, 136, 0.3);
  }

  .state-connected:hover {
    border-color: rgba(0, 255, 136, 0.6);
    box-shadow: 0 0 15px rgba(0, 255, 136, 0.1);
  }

  .state-reconnecting {
    border-color: rgba(251, 191, 36, 0.3);
  }

  .state-reconnecting:hover {
    border-color: rgba(251, 191, 36, 0.6);
    box-shadow: 0 0 15px rgba(251, 191, 36, 0.1);
  }

  .arrows-svg {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    z-index: 0;
    pointer-events: none;
  }

  /* Info cards */
  .info-cards {
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out, transform 0.6s ease-out;
  }

  .info-cards.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .glass-card {
    background: rgba(26, 26, 46, 0.6);
    border: 1px solid var(--color-border);
    border-radius: 12px;
    backdrop-filter: blur(12px);
    padding: 1.25rem;
    display: flex;
    gap: 1rem;
    align-items: flex-start;
    transition: border-color 0.3s ease;
  }

  .glass-card:hover {
    border-color: rgba(56, 189, 248, 0.3);
  }

  .info-icon {
    width: 36px;
    height: 36px;
    flex-shrink: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 8px;
  }

  .info-icon svg {
    width: 20px;
    height: 20px;
  }

  .info-icon.accent-sky {
    background: rgba(56, 189, 248, 0.1);
    color: #38bdf8;
  }

  .info-icon.accent-green {
    background: rgba(0, 255, 136, 0.1);
    color: var(--color-accent-primary);
  }

  .info-text {
    color: var(--color-text-secondary);
    font-size: 0.9rem;
    line-height: 1.6;
  }

  /* Responsive */
  @media (max-width: 768px) {
    .architecture-grid {
      grid-template-columns: 1fr;
      gap: 2rem;
    }

    .layer-box {
      padding: 0.75rem 1rem;
    }

    .layer-name {
      font-size: 0.85rem;
    }

    .layer-desc {
      font-size: 0.75rem;
    }

    .state-bubble {
      font-size: 0.72rem;
      padding: 0.5rem 0.75rem;
    }

    .states-grid {
      gap: 2.5rem 1rem;
    }

    .state-diagram {
      padding: 1.5rem 1rem;
    }

    .gradient-orb-1,
    .gradient-orb-2 {
      opacity: 0.05;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .section-title,
    .section-subtitle,
    .layers-diagram,
    .state-machine,
    .info-cards {
      transition: none;
      opacity: 1;
      transform: none;
    }

    .gradient-orb-1,
    .gradient-orb-2 {
      animation: none;
    }

    .layer-box:hover {
      transform: none;
    }

    .connecting-dot,
    .reconnecting-dot {
      animation: none;
    }
  }
</style>
