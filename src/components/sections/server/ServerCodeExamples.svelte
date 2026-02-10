<script lang="ts">
  import { onMount } from 'svelte';

  interface ServerCodeExamplesTranslations {
    title: string;
    subtitle: string;
    tabs: {
      startup: string;
      crud: string;
      subscriptions: string;
      transactions: string;
    };
  }

  interface Props {
    translations: ServerCodeExamplesTranslations;
  }

  let { translations }: Props = $props();

  let sectionRef: HTMLElement | null = $state(null);
  let isVisible = $state(false);
  let activeTab = $state<'startup' | 'crud' | 'subscriptions' | 'transactions'>('startup');

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
    startup: {
      filename: 'server.ts',
      code: `import { NoexServer } from '@hamicek/noex-server';
import { Store } from '@hamicek/noex-store';
import { RuleEngine } from '@hamicek/noex-rules';

const store = await Store.start();
await store.defineBucket('users', {
  key: 'id',
  schema: {
    id:   { type: 'string', generated: 'uuid' },
    name: { type: 'string', required: true },
  },
});

const rules = await RuleEngine.start();

const server = await NoexServer.start({
  store,
  rules,
  port: 8080,
  auth: {
    validate: async (token) => {
      // Return session or null
      return { userId: 'user_1', roles: ['admin'] };
    },
  },
  heartbeat: { intervalMs: 30_000, timeoutMs: 10_000 },
  backpressure: { maxBufferedBytes: 1_048_576, highWaterMark: 0.8 },
});

console.log(\`Server listening on port \${server.port}\`);`,
    },
    crud: {
      filename: 'client-crud.json',
      code: `// Insert a record
\u2192 { "id": 1, "type": "store.insert", "bucket": "users",
    "data": { "name": "Alice" } }
\u2190 { "id": 1, "type": "result",
    "data": { "id": "abc-123", "name": "Alice" } }

// Get by key
\u2192 { "id": 2, "type": "store.get", "bucket": "users", "key": "abc-123" }
\u2190 { "id": 2, "type": "result",
    "data": { "id": "abc-123", "name": "Alice" } }

// Update
\u2192 { "id": 3, "type": "store.update", "bucket": "users",
    "key": "abc-123", "data": { "name": "Alice Smith" } }
\u2190 { "id": 3, "type": "result",
    "data": { "id": "abc-123", "name": "Alice Smith" } }

// Delete
\u2192 { "id": 4, "type": "store.delete", "bucket": "users",
    "key": "abc-123" }
\u2190 { "id": 4, "type": "result", "data": true }`,
    },
    subscriptions: {
      filename: 'client-subscriptions.json',
      code: `// Subscribe to a reactive query
\u2192 { "id": 5, "type": "store.subscribe", "query": "activeUsers" }
\u2190 { "id": 5, "type": "result",
    "data": { "subscriptionId": "sub_1", "data": [...] } }

// Server pushes updates when data changes
\u2190 { "type": "push", "channel": "subscription",
    "subscriptionId": "sub_1",
    "data": [{ "id": "abc", "name": "Alice", "status": "active" }] }

// Subscribe to rules events
\u2192 { "id": 6, "type": "rules.subscribe", "pattern": "user.*" }
\u2190 { "id": 6, "type": "result",
    "data": { "subscriptionId": "sub_2" } }

// Server pushes rule events
\u2190 { "type": "push", "channel": "event",
    "subscriptionId": "sub_2",
    "data": { "topic": "user.login", "event": { "data": { "userId": "abc" } } } }

// Unsubscribe
\u2192 { "id": 7, "type": "store.unsubscribe",
    "subscriptionId": "sub_1" }
\u2190 { "id": 7, "type": "result", "data": true }`,
    },
    transactions: {
      filename: 'client-transaction.json',
      code: `// Multi-bucket atomic transaction
\u2192 { "id": 8, "type": "store.transaction", "operations": [
    { "op": "get", "bucket": "accounts", "key": "alice" },
    { "op": "get", "bucket": "accounts", "key": "bob" },
    { "op": "update", "bucket": "accounts",
      "key": "alice", "data": { "balance": 900 } },
    { "op": "update", "bucket": "accounts",
      "key": "bob", "data": { "balance": 1100 } }
  ]
}

// All operations succeed or all are rolled back
\u2190 { "id": 8, "type": "result", "data": {
    "results": [
      { "index": 0, "data": { "id": "alice", "balance": 1000 } },
      { "index": 1, "data": { "id": "bob", "balance": 1000 } },
      { "index": 2, "data": { "id": "alice", "balance": 900 } },
      { "index": 3, "data": { "id": "bob", "balance": 1100 } }
    ]
  }
}

// On conflict (optimistic lock failure)
\u2190 { "id": 9, "type": "error",
    "code": "CONFLICT",
    "message": "Transaction conflict: version mismatch" }`,
    },
  };

  function highlightCode(code: string): string {
    return code
      // Comments first
      .replace(/(\/\/[^\n]*)/g, '<span class="hl-comment">$1</span>')
      // Strings (double-quoted in JSON)
      .replace(/"([^"]*?)"/g, '<span class="hl-string">"$1"</span>')
      // Single-quoted strings (in TypeScript)
      .replace(/'([^']*?)'/g, '<span class="hl-string">\'$1\'</span>')
      // Keywords
      .replace(/\b(import|from|export|const|let|return|new|await|async|function|class|true|false|null)\b/g, '<span class="hl-keyword">$1</span>')
      // Classes
      .replace(/\b(NoexServer|Store|RuleEngine)\b/g, '<span class="hl-class">$1</span>')
      // Numbers
      .replace(/\b(\d[\d_]*)\b/g, '<span class="hl-number">$1</span>')
      // Methods
      .replace(/\b(start|defineBucket|subscribe|unsubscribe)\b(?=\s*[\(\<])/g, '<span class="hl-method">$1</span>')
      // Template literals
      .replace(/`([^`]*)`/g, '<span class="hl-string">`$1`</span>')
      // Arrows
      .replace(/(\u2192)/g, '<span class="hl-arrow-out">$1</span>')
      .replace(/(\u2190)/g, '<span class="hl-arrow-in">$1</span>');
  }

  const tabs = $derived([
    { key: 'startup' as const, label: translations.tabs.startup },
    { key: 'crud' as const, label: translations.tabs.crud },
    { key: 'subscriptions' as const, label: translations.tabs.subscriptions },
    { key: 'transactions' as const, label: translations.tabs.transactions },
  ]);
</script>

<section
  bind:this={sectionRef}
  id="server-examples"
  class="examples-section snap-section bg-bg-secondary"
  aria-labelledby="server-examples-title"
>
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-blob gradient-blob-1"></div>
    <div class="gradient-blob gradient-blob-2"></div>
    <div class="grid-pattern"></div>
  </div>

  <div class="content-wrapper max-w-4xl mx-auto relative z-10">
    <h2
      id="server-examples-title"
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

    <div class="code-container glass-card" class:visible={isVisible}>
      <div class="tab-bar" role="tablist" aria-label="Code examples">
        {#each tabs as tab}
          <button
            class="tab-button"
            class:active={activeTab === tab.key}
            role="tab"
            aria-selected={activeTab === tab.key}
            aria-controls="tabpanel-{tab.key}"
            onclick={() => { activeTab = tab.key; }}
          >
            {tab.label}
          </button>
        {/each}
      </div>

      {#each tabs as tab}
        {#if activeTab === tab.key}
          <div
            id="tabpanel-{tab.key}"
            class="code-panel"
            role="tabpanel"
            aria-labelledby="tab-{tab.key}"
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
  .examples-section {
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
    background: var(--color-accent-primary);
    top: -200px;
    left: -200px;
    animation: blobFloat1 15s ease-in-out infinite;
  }

  .gradient-blob-2 {
    width: 400px;
    height: 400px;
    background: #a78bfa;
    bottom: -200px;
    right: -200px;
    animation: blobFloat2 18s ease-in-out infinite;
  }

  .grid-pattern {
    position: absolute;
    inset: 0;
    background-image:
      linear-gradient(rgba(167, 139, 250, 0.02) 1px, transparent 1px),
      linear-gradient(90deg, rgba(167, 139, 250, 0.02) 1px, transparent 1px);
    background-size: 40px 40px;
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
    color: #a78bfa;
    border-bottom-color: #a78bfa;
    background: rgba(167, 139, 250, 0.05);
  }

  .tab-button:focus-visible {
    outline: 2px solid #a78bfa;
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
    color: #a78bfa;
  }

  .code-content :global(.hl-class) {
    color: var(--color-accent-primary);
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

  .code-content :global(.hl-comment) {
    color: #6a9955;
    font-style: italic;
  }

  .code-content :global(.hl-arrow-out) {
    color: #a78bfa;
    font-weight: 700;
  }

  .code-content :global(.hl-arrow-in) {
    color: var(--color-accent-primary);
    font-weight: 700;
  }

  @media (max-width: 768px) {
    .tab-button {
      padding: 0.75rem 0.5rem;
      font-size: 0.75rem;
    }

    .code-content {
      font-size: 0.7rem;
      padding: 1rem;
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
