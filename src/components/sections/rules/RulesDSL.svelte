<script lang="ts">
  import { onMount } from 'svelte';

  interface RulesDSLTranslations {
    title: string;
    subtitle: string;
    tabs: {
      fluent: string;
      template: string;
      yaml: string;
    };
  }

  interface Props {
    translations: RulesDSLTranslations;
  }

  let { translations }: Props = $props();

  let sectionRef: HTMLElement | null = $state(null);
  let isVisible = $state(false);
  let activeTab = $state<'fluent' | 'template' | 'yaml'>('fluent');

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
      { threshold: 0.15 }
    );

    observer.observe(sectionRef);
    return () => observer.disconnect();
  });

  const codeExamples: Record<string, { code: string; filename: string }> = {
    fluent: {
      filename: 'rules.ts',
      code: `${'import'} { Rule, onEvent, event, emit, setFact, ref }
  from '@hamicek/noex-rules/dsl';

const rule = Rule.create('order-notification')
  .description('Notify on high-value orders')
  .priority(10)
  .when(onEvent('order.created'))
  .if(event('amount').gte(100))
  .then(
    emit('notification.send', {
      orderId: ref('event.orderId'),
      message: 'High-value order received',
    })
  )
  .also(
    setFact('order:\${event.orderId}:notified', true)
  )
  .build();`,
    },
    template: {
      filename: 'rules.ts',
      code: `${'import'} { rule } from '@hamicek/noex-rules/dsl';

const orderNotification = rule\`
  rule "order-notification"
  priority 10
  when event "order.created"
  if event.amount >= 100
  then emit "notification.send" {
    orderId: event.orderId,
    message: "High-value order received"
  }
  then set_fact "order:\${event.orderId}:notified" true
\`;`,
    },
    yaml: {
      filename: 'rules.yaml',
      code: `rules:
  - name: order-notification
    description: Notify on high-value orders
    priority: 10
    trigger:
      type: event
      topic: order.created
    conditions:
      - field: event.amount
        operator: gte
        value: 100
    actions:
      - type: emit_event
        topic: notification.send
        data:
          orderId: "\${event.orderId}"
          message: High-value order received
      - type: set_fact
        key: "order:\${event.orderId}:notified"
        value: true`,
    },
  };

  function highlightCode(code: string): string {
    return code
      .replace(/(\/\/[^\n]*)/g, '<span class="hl-comment">$1</span>')
      .replace(/(#[^\n]*)/g, '<span class="hl-comment">$1</span>')
      .replace(/'([^']*?)'/g, '<span class="hl-string">\'$1\'</span>')
      .replace(/"([^"]*?)"/g, '<span class="hl-string">"$1"</span>')
      .replace(/\b(import|from|export|const|let|return|new|await|async|function|true|false)\b/g, '<span class="hl-keyword">$1</span>')
      .replace(/\b(Rule|RuleEngine)\b/g, '<span class="hl-class">$1</span>')
      .replace(/\b(\d+)\b/g, '<span class="hl-number">$1</span>')
      .replace(/\b(create|description|priority|when|if|then|also|build|onEvent|event|emit|setFact|ref)\b(?=\s*[\(\<`])/g, '<span class="hl-method">$1</span>')
      .replace(/^(\s*[\w-]+)(?=:)/gm, '<span class="hl-property">$1</span>')
      .replace(/=&gt;/g, '<span class="hl-keyword">=&gt;</span>');
  }

  const tabs = $derived([
    { key: 'fluent' as const, label: translations.tabs.fluent },
    { key: 'template' as const, label: translations.tabs.template },
    { key: 'yaml' as const, label: translations.tabs.yaml },
  ]);
</script>

<section
  bind:this={sectionRef}
  id="rules-dsl"
  class="dsl-section snap-section bg-bg-secondary"
  aria-labelledby="rules-dsl-title"
>
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-blob gradient-blob-1"></div>
    <div class="gradient-blob gradient-blob-2"></div>
    <div class="dot-pattern"></div>
  </div>

  <div class="content-wrapper w-full max-w-4xl mx-auto relative z-10">
    <h2
      id="rules-dsl-title"
      class="section-title text-3xl md:text-5xl font-bold mb-4 text-text-primary text-center"
      class:visible={isVisible}
    >
      {translations.title}
    </h2>

    <p
      class="section-subtitle text-lg text-text-secondary text-center mb-10 md:mb-14 max-w-2xl mx-auto"
      class:visible={isVisible}
    >
      {translations.subtitle}
    </p>

    <!-- Tabbed Code Block -->
    <div class="code-container glass-card" class:visible={isVisible}>
      <div class="tab-bar" role="tablist" aria-label="DSL examples">
        {#each tabs as tab}
          <button
            class="tab-button"
            class:active={activeTab === tab.key}
            role="tab"
            aria-selected={activeTab === tab.key}
            aria-controls="dsl-tabpanel-{tab.key}"
            onclick={() => { activeTab = tab.key; }}
          >
            {tab.label}
          </button>
        {/each}
      </div>

      {#each tabs as tab}
        {#if activeTab === tab.key}
          <div
            id="dsl-tabpanel-{tab.key}"
            class="code-panel"
            role="tabpanel"
            aria-labelledby="dsl-tab-{tab.key}"
          >
            <div class="code-header">
              <div class="terminal-dots">
                <span class="dot dot-red"></span>
                <span class="dot dot-yellow"></span>
                <span class="dot dot-green"></span>
              </div>
              <span class="code-filename">{codeExamples[tab.key].filename}</span>
            </div>
            <pre class="code-content"><code>{@html highlightCode(codeExamples[tab.key].code)}</code></pre>
          </div>
        {/if}
      {/each}
    </div>
  </div>
</section>

<style>
  .dsl-section {
    position: relative;
    overflow: hidden;
  }

  .background-elements {
    position: absolute;
    inset: 0;
    pointer-events: none;
  }

  .gradient-blob {
    position: absolute;
    border-radius: 50%;
    filter: blur(120px);
    opacity: 0.08;
  }

  .gradient-blob-1 {
    width: 500px;
    height: 500px;
    background: var(--color-accent-secondary);
    top: -200px;
    left: -200px;
    animation: blobFloat1 15s ease-in-out infinite;
  }

  .gradient-blob-2 {
    width: 400px;
    height: 400px;
    background: var(--color-accent-primary);
    bottom: -200px;
    right: -200px;
    animation: blobFloat2 18s ease-in-out infinite;
  }

  .dot-pattern {
    position: absolute;
    inset: 0;
    background-image: radial-gradient(rgba(0, 255, 136, 0.06) 1px, transparent 1px);
    background-size: 24px 24px;
  }

  @keyframes blobFloat1 {
    0%, 100% { transform: translate(0, 0) scale(1); }
    50% { transform: translate(-40px, 30px) scale(1.05); }
  }

  @keyframes blobFloat2 {
    0%, 100% { transform: translate(0, 0) scale(1); }
    50% { transform: translate(30px, -40px) scale(1.08); }
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

  .glass-card {
    background: rgba(18, 18, 26, 0.8);
    border: 1px solid var(--color-border);
    border-radius: 16px;
    backdrop-filter: blur(12px);
    overflow: hidden;
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out 0.25s, transform 0.6s ease-out 0.25s;
  }

  .glass-card.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .tab-bar {
    display: flex;
    border-bottom: 1px solid var(--color-border);
    background: rgba(26, 26, 46, 0.5);
  }

  .tab-button {
    flex: 1;
    padding: 0.875rem 1.25rem;
    font-size: 0.875rem;
    font-weight: 500;
    color: var(--color-text-secondary);
    background: transparent;
    border: none;
    border-bottom: 2px solid transparent;
    cursor: pointer;
    transition: all 0.2s ease;
    text-align: center;
  }

  .tab-button:hover {
    color: var(--color-text-primary);
    background: rgba(255, 255, 255, 0.03);
  }

  .tab-button.active {
    color: var(--color-accent-primary);
    border-bottom-color: var(--color-accent-primary);
    background: rgba(0, 255, 136, 0.05);
  }

  .tab-button:focus-visible {
    outline: 2px solid var(--color-accent-primary);
    outline-offset: -2px;
  }

  .code-panel {
    animation: fadeIn 0.2s ease-out;
  }

  @keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
  }

  .code-header {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px 16px;
    border-bottom: 1px solid var(--color-border);
    background: rgba(255, 255, 255, 0.02);
  }

  .terminal-dots {
    display: flex;
    gap: 6px;
  }

  .dot {
    width: 10px;
    height: 10px;
    border-radius: 50%;
  }

  .dot-red { background: #ff5f56; }
  .dot-yellow { background: #ffbd2e; }
  .dot-green { background: #27ca40; }

  .code-filename {
    margin-left: auto;
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: 0.75rem;
    color: var(--color-text-secondary);
  }

  .code-content {
    padding: 1.25rem 1.5rem;
    margin: 0;
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: 0.8rem;
    line-height: 1.7;
    color: var(--color-text-secondary);
    overflow-x: auto;
    max-height: 480px;
    overflow-y: auto;
  }

  .code-content code {
    display: block;
    white-space: pre;
  }

  .code-content :global(.hl-keyword) {
    color: var(--color-accent-primary);
  }

  .code-content :global(.hl-class) {
    color: var(--color-accent-secondary);
  }

  .code-content :global(.hl-method) {
    color: #dcdcaa;
  }

  .code-content :global(.hl-string) {
    color: #ce9178;
  }

  .code-content :global(.hl-number) {
    color: #b5cea8;
  }

  .code-content :global(.hl-property) {
    color: #9cdcfe;
  }

  .code-content :global(.hl-comment) {
    color: #6a9955;
    font-style: italic;
  }

  @media (max-width: 768px) {
    .tab-button {
      padding: 0.75rem 0.5rem;
      font-size: 0.8rem;
    }

    .code-content {
      font-size: 0.75rem;
      padding: 1rem 0.75rem;
      max-height: 400px;
    }

    .gradient-blob-1,
    .gradient-blob-2 {
      opacity: 0.05;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .section-title,
    .section-subtitle,
    .glass-card {
      transition: none;
      opacity: 1;
      transform: none;
    }

    .code-panel {
      animation: none;
    }

    .gradient-blob-1,
    .gradient-blob-2 {
      animation: none;
    }
  }
</style>
