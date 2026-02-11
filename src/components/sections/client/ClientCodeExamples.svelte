<script lang="ts">
  import { onMount } from 'svelte';

  interface ClientCodeExamplesTranslations {
    title: string;
    subtitle: string;
    tabs: {
      connection: string;
      crud: string;
      subscriptions: string;
      transactions: string;
      rules: string;
    };
  }

  interface Props {
    translations: ClientCodeExamplesTranslations;
  }

  let { translations }: Props = $props();

  let sectionRef: HTMLElement | null = $state(null);
  let isVisible = $state(false);
  let activeTab = $state<'connection' | 'crud' | 'subscriptions' | 'transactions' | 'rules'>('connection');

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
    connection: {
      filename: 'client.ts',
      code: `${'import'} { NoexClient } from '@hamicek/noex-client';

const client = new NoexClient('ws://localhost:8080', {
  auth: { token: 'my-secret-token' },
  reconnect: {
    maxRetries: Infinity,
    initialDelayMs: 1_000,
    maxDelayMs: 30_000,
    backoffMultiplier: 2,
    jitterMs: 500,
  },
  requestTimeoutMs: 10_000,
  connectTimeoutMs: 5_000,
  heartbeat: true,
});

const info = await client.connect();
console.log(\`Connected to v\${info.version}\`);

client.on('reconnecting', (attempt) => {
  console.log(\`Reconnecting... attempt \${attempt}\`);
});

client.on('reconnected', () => {
  console.log('Connection restored!');
});

// Graceful disconnect
await client.disconnect();`,
    },
    crud: {
      filename: 'crud.ts',
      code: `interface User {
  id: string;
  name: string;
  email: string;
}

const users = client.store.bucket<User>('users');

// Insert
const alice = await users.insert({
  name: 'Alice',
  email: 'alice@example.com',
});

// Get by key
const user = await users.get(alice.id);

// Update
await users.update(alice.id, { name: 'Alice Smith' });

// Query
const results = await users.where({ name: 'Alice Smith' });
const one = await users.findOne({ email: 'alice@example.com' });

// Pagination & aggregation
const page = await users.paginate({ limit: 10 });
const total = await users.count();
const avgAge = await users.avg('age');

// Delete
await users.delete(alice.id);`,
    },
    subscriptions: {
      filename: 'subscriptions.ts',
      code: `// Store subscription — initial data + live updates
const unsubscribe = await client.store.subscribe(
  'activeUsers',
  (data) => {
    console.log('Active users:', data);
  },
);

// With query parameters
const unsub2 = await client.store.subscribe(
  'usersByRole',
  { role: 'admin' },
  (data) => {
    console.log('Admins:', data);
  },
);

// Unsubscribe when done
unsubscribe();
unsub2();`,
    },
    transactions: {
      filename: 'transaction.ts',
      code: `// Atomic multi-bucket transfer
const result = await client.store.transaction([
  { op: 'get', bucket: 'accounts', key: 'alice' },
  { op: 'get', bucket: 'accounts', key: 'bob' },
  { op: 'update', bucket: 'accounts',
    key: 'alice', data: { balance: 900 } },
  { op: 'update', bucket: 'accounts',
    key: 'bob', data: { balance: 1100 } },
]);

// All operations succeed or all are rolled back
console.log(result.results);
// [{ index: 0, data: {...} }, { index: 1, data: {...} }, ...]`,
    },
    rules: {
      filename: 'rules.ts',
      code: `// Emit an event
const event = await client.rules.emit('order:placed', {
  orderId: 'ord_123',
  amount: 99.99,
});

// Subscribe to pattern-matched events
const unsub = await client.rules.subscribe(
  'order:*',
  (event, topic) => {
    console.log(\`[\${topic}]\`, event.data);
  },
);

// Manage facts
await client.rules.setFact('user:1:tier', 'premium');
const tier = await client.rules.getFact('user:1:tier');
const userFacts = await client.rules.queryFacts('user:1:*');

await client.rules.deleteFact('user:1:tier');`,
    },
  };

  function highlightCode(code: string): string {
    return code
      // Comments first
      .replace(/(\/\/[^\n]*)/g, '<span class="hl-comment">$1</span>')
      // Strings (double-quoted)
      .replace(/"([^"]*?)"/g, '<span class="hl-string">"$1"</span>')
      // Single-quoted strings
      .replace(/'([^']*?)'/g, '<span class="hl-string">\'$1\'</span>')
      // Keywords
      .replace(/\b(import|from|export|const|let|return|new|await|async|function|class|interface|true|false|null)\b/g, '<span class="hl-keyword">$1</span>')
      // Classes
      .replace(/\b(NoexClient|User)\b/g, '<span class="hl-class">$1</span>')
      // Numbers
      .replace(/\b(\d[\d_]*)\b/g, '<span class="hl-number">$1</span>')
      // Methods
      .replace(/\b(connect|disconnect|bucket|subscribe|unsubscribe|insert|get|update|delete|where|findOne|paginate|count|avg|transaction|emit|setFact|getFact|queryFacts|deleteFact)\b(?=\s*[\(\<])/g, '<span class="hl-method">$1</span>')
      // Template literals
      .replace(/`([^`]*)`/g, '<span class="hl-string">`$1`</span>');
  }

  const tabs = $derived([
    { key: 'connection' as const, label: translations.tabs.connection },
    { key: 'crud' as const, label: translations.tabs.crud },
    { key: 'subscriptions' as const, label: translations.tabs.subscriptions },
    { key: 'transactions' as const, label: translations.tabs.transactions },
    { key: 'rules' as const, label: translations.tabs.rules },
  ]);
</script>

<section
  bind:this={sectionRef}
  id="client-examples"
  class="examples-section snap-section bg-bg-secondary"
  aria-labelledby="client-examples-title"
>
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-blob gradient-blob-1"></div>
    <div class="gradient-blob gradient-blob-2"></div>
    <div class="grid-pattern"></div>
  </div>

  <div class="content-wrapper w-full max-w-4xl mx-auto relative z-10">
    <h2
      id="client-examples-title"
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
    background: #38bdf8;
    bottom: -200px;
    right: -200px;
    animation: blobFloat2 18s ease-in-out infinite;
  }

  .grid-pattern {
    position: absolute;
    inset: 0;
    background-image:
      linear-gradient(rgba(56, 189, 248, 0.02) 1px, transparent 1px),
      linear-gradient(90deg, rgba(56, 189, 248, 0.02) 1px, transparent 1px);
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
    color: #38bdf8;
    border-bottom-color: #38bdf8;
    background: rgba(56, 189, 248, 0.05);
  }

  .tab-button:focus-visible {
    outline: 2px solid #38bdf8;
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
    color: #38bdf8;
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

  @media (max-width: 768px) {
    .tab-button {
      padding: 0.75rem 0.375rem;
      font-size: 0.72rem;
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
