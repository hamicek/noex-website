<script lang="ts">
  import { onMount } from 'svelte';

  interface ServerProtocolTranslations {
    title: string;
    subtitle: string;
    lifecycle: string;
    lifecycleSteps: {
      connect: string;
      welcome: string;
      auth: string;
      requestResponse: string;
      push: string;
      disconnect: string;
    };
    messageTypes: {
      title: string;
      request: string;
      response: string;
      push: string;
      heartbeat: string;
    };
    tabs: {
      storeOps: string;
      rulesOps: string;
      authOps: string;
      serverOps: string;
    };
    storeOps: string[];
    rulesOps: string[];
    authOps: string[];
    serverOps: string[];
  }

  interface Props {
    translations: ServerProtocolTranslations;
  }

  let { translations }: Props = $props();

  let sectionRef: HTMLElement | null = $state(null);
  let isVisible = $state(false);
  let activeTab = $state<'store' | 'rules' | 'auth' | 'server'>('store');

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

  const lifecycleSteps = $derived([
    { key: 'connect', icon: 'plug' },
    { key: 'welcome', icon: 'wave' },
    { key: 'auth', icon: 'key' },
    { key: 'requestResponse', icon: 'arrows' },
    { key: 'push', icon: 'bell' },
    { key: 'disconnect', icon: 'x' },
  ] as const);

  const tabs = $derived([
    { key: 'store' as const, label: translations.tabs.storeOps },
    { key: 'rules' as const, label: translations.tabs.rulesOps },
    { key: 'auth' as const, label: translations.tabs.authOps },
    { key: 'server' as const, label: translations.tabs.serverOps },
  ]);

  function getOpsForTab(tab: 'store' | 'rules' | 'auth' | 'server'): string[] {
    const prefix = { store: 'store.', rules: 'rules.', auth: 'auth.', server: 'server.' }[tab];
    const ops = {
      store: translations.storeOps,
      rules: translations.rulesOps,
      auth: translations.authOps,
      server: translations.serverOps,
    }[tab];
    return ops.map((op) => `${prefix}${op}`);
  }

  const messageExamples = {
    request: '{ "id": 1, "type": "store.get", "bucket": "users", "key": "abc" }',
    response: '{ "id": 1, "type": "result", "data": { "id": "abc", "name": "Alice" } }',
    push: '{ "type": "push", "channel": "subscription", "subscriptionId": "sub_1", "data": [...] }',
    heartbeat: '{ "type": "ping", "timestamp": 1706000000 }',
  };
</script>

<section
  bind:this={sectionRef}
  id="server-protocol"
  class="protocol-section snap-section bg-bg-secondary"
  aria-labelledby="server-protocol-title"
>
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-orb gradient-orb-1"></div>
    <div class="gradient-orb gradient-orb-2"></div>
    <div class="dot-pattern"></div>
  </div>

  <div class="content-wrapper max-w-6xl mx-auto relative z-10">
    <h2
      id="server-protocol-title"
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

    <div class="protocol-grid" class:visible={isVisible}>
      <!-- Message Types -->
      <div class="message-types glass-card">
        <h3 class="card-section-title">{translations.messageTypes.title}</h3>
        <div class="message-examples">
          {#each Object.entries(messageExamples) as [key, example]}
            <div class="message-example">
              <div class="message-label">
                <span class="message-direction" class:outgoing={key === 'request' || key === 'heartbeat'}>
                  {key === 'request' || key === 'heartbeat' ? 'Client \u2192' : '\u2190 Server'}
                </span>
                <span class="message-type-name">
                  {translations.messageTypes[key as keyof typeof translations.messageTypes]}
                </span>
              </div>
              <code class="message-code">{example}</code>
            </div>
          {/each}
        </div>
      </div>

      <!-- Operations -->
      <div class="operations glass-card">
        <div class="ops-tab-bar" role="tablist">
          {#each tabs as tab}
            <button
              class="ops-tab"
              class:active={activeTab === tab.key}
              role="tab"
              aria-selected={activeTab === tab.key}
              onclick={() => { activeTab = tab.key; }}
            >
              {tab.label}
            </button>
          {/each}
        </div>
        <div class="ops-list">
          {#each getOpsForTab(activeTab) as op}
            <span class="ops-badge">{op}</span>
          {/each}
        </div>
      </div>
    </div>

    <!-- Lifecycle -->
    <div class="lifecycle-container" class:visible={isVisible}>
      <h3 class="lifecycle-title">{translations.lifecycle}</h3>
      <div class="lifecycle-steps">
        {#each lifecycleSteps as step, i}
          <div class="lifecycle-step">
            <div class="step-number">{i + 1}</div>
            <span class="step-label">{translations.lifecycleSteps[step.key]}</span>
          </div>
          {#if i < lifecycleSteps.length - 1}
            <div class="step-connector" aria-hidden="true">
              <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="connector-arrow">
                <path stroke-linecap="round" stroke-linejoin="round" d="M13.5 4.5L21 12m0 0l-7.5 7.5M21 12H3" />
              </svg>
            </div>
          {/if}
        {/each}
      </div>
    </div>
  </div>
</section>

<style>
  .protocol-section {
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
    background: #a78bfa;
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
    background-image: radial-gradient(rgba(167, 139, 250, 0.06) 1px, transparent 1px);
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

  .protocol-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.5rem;
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out 0.3s, transform 0.6s ease-out 0.3s;
  }

  .protocol-grid.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .glass-card {
    background: rgba(26, 26, 46, 0.6);
    border: 1px solid var(--color-border);
    border-radius: 16px;
    backdrop-filter: blur(12px);
    padding: 1.5rem;
  }

  .card-section-title {
    font-size: 1.1rem;
    font-weight: 600;
    color: #a78bfa;
    margin-bottom: 1.25rem;
  }

  .message-examples {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }

  .message-example {
    display: flex;
    flex-direction: column;
    gap: 0.375rem;
  }

  .message-label {
    display: flex;
    align-items: center;
    gap: 0.5rem;
  }

  .message-direction {
    font-size: 0.7rem;
    font-weight: 600;
    color: var(--color-accent-primary);
    padding: 0.125rem 0.5rem;
    background: rgba(0, 255, 136, 0.1);
    border-radius: 4px;
  }

  .message-direction.outgoing {
    color: #a78bfa;
    background: rgba(167, 139, 250, 0.1);
  }

  .message-type-name {
    font-size: 0.8rem;
    font-weight: 500;
    color: var(--color-text-primary);
  }

  .message-code {
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: 0.7rem;
    color: var(--color-text-secondary);
    background: rgba(18, 18, 26, 0.6);
    padding: 0.5rem 0.75rem;
    border-radius: 8px;
    border: 1px solid rgba(255, 255, 255, 0.04);
    overflow-x: auto;
    white-space: nowrap;
  }

  /* Operations */
  .ops-tab-bar {
    display: flex;
    border-bottom: 1px solid var(--color-border);
    margin: -1.5rem -1.5rem 1.25rem -1.5rem;
    padding: 0;
  }

  .ops-tab {
    flex: 1;
    padding: 0.75rem 0.5rem;
    font-size: 0.8rem;
    font-weight: 500;
    color: var(--color-text-secondary);
    background: transparent;
    border: none;
    border-bottom: 2px solid transparent;
    cursor: pointer;
    transition: all 0.2s ease;
    text-align: center;
  }

  .ops-tab:hover {
    color: var(--color-text-primary);
    background: rgba(255, 255, 255, 0.03);
  }

  .ops-tab.active {
    color: #a78bfa;
    border-bottom-color: #a78bfa;
    background: rgba(167, 139, 250, 0.05);
  }

  .ops-tab:focus-visible {
    outline: 2px solid #a78bfa;
    outline-offset: -2px;
  }

  .ops-list {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
  }

  .ops-badge {
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: 0.75rem;
    padding: 0.25rem 0.625rem;
    background: rgba(167, 139, 250, 0.08);
    border: 1px solid rgba(167, 139, 250, 0.2);
    border-radius: 6px;
    color: #c4b5fd;
    white-space: nowrap;
  }

  /* Lifecycle */
  .lifecycle-container {
    margin-top: 2rem;
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out 0.5s, transform 0.6s ease-out 0.5s;
  }

  .lifecycle-container.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .lifecycle-title {
    font-size: 1.1rem;
    font-weight: 600;
    color: var(--color-text-primary);
    text-align: center;
    margin-bottom: 1.25rem;
  }

  .lifecycle-steps {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
    gap: 0.375rem;
  }

  .lifecycle-step {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.5rem 0.875rem;
    background: rgba(26, 26, 46, 0.6);
    border: 1px solid var(--color-border);
    border-radius: 8px;
    transition: border-color 0.2s ease;
  }

  .lifecycle-step:hover {
    border-color: rgba(167, 139, 250, 0.4);
  }

  .step-number {
    width: 22px;
    height: 22px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;
    background: rgba(167, 139, 250, 0.15);
    color: #a78bfa;
    font-size: 0.7rem;
    font-weight: 600;
    flex-shrink: 0;
  }

  .step-label {
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
    color: rgba(167, 139, 250, 0.4);
  }

  @media (max-width: 1024px) {
    .protocol-grid {
      grid-template-columns: 1fr;
    }
  }

  @media (max-width: 768px) {
    .lifecycle-steps {
      flex-direction: column;
    }

    .step-connector {
      transform: rotate(90deg);
    }

    .gradient-orb-1,
    .gradient-orb-2 {
      opacity: 0.05;
    }

    .ops-tab {
      font-size: 0.7rem;
      padding: 0.625rem 0.25rem;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .section-title,
    .section-subtitle,
    .protocol-grid,
    .lifecycle-container {
      transition: none;
      opacity: 1;
      transform: none;
    }

    .gradient-orb-1,
    .gradient-orb-2 {
      animation: none;
    }
  }
</style>
