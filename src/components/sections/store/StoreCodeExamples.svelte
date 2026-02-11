<script lang="ts">
  import { onMount } from 'svelte';

  interface StoreCodeExamplesTranslations {
    title: string;
    subtitle: string;
    tabs: {
      schema: string;
      queries: string;
      transactions: string;
    };
  }

  interface Props {
    translations: StoreCodeExamplesTranslations;
  }

  let { translations }: Props = $props();

  let sectionRef: HTMLElement | null = $state(null);
  let isVisible = $state(false);
  let activeTab = $state<'schema' | 'queries' | 'transactions'>('schema');

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
    schema: {
      filename: 'schema.ts',
      code: `${'import'} { Store } from '@hamicek/noex-store';

const store = await Store.start();

await store.defineBucket('users', {
  key: 'id',
  schema: {
    id:    { type: 'string', generated: 'uuid' },
    name:  { type: 'string', required: true },
    email: { type: 'string', format: 'email', unique: true },
    role:  { type: 'string', enum: ['admin', 'user'], default: 'user' },
  },
});

const users = store.bucket('users');

// Insert — validated against schema, id auto-generated
const alice = await users.insert({
  name: 'Alice',
  email: 'alice@example.com',
});

// Read
const user = await users.get(alice.id);

// Update
await users.update(alice.id, { role: 'admin' });

// Query
const admins = await users.where({ role: 'admin' });`,
    },
    queries: {
      filename: 'queries.ts',
      code: `// Define a reactive query
store.defineQuery('activeUsers', async (ctx) => {
  return ctx.bucket('users').where({ status: 'active' });
});

// Get initial result
const users = await store.runQuery('activeUsers');

// Subscribe to changes — re-evaluates automatically
const unsubscribe = await store.subscribe(
  'activeUsers',
  (result) => {
    console.log('Active users changed:', result.length);
  },
);

// This triggers the query to re-evaluate
await store.bucket('users').insert({
  name: 'Bob',
  status: 'active',
});

// Parameterized query
store.defineQuery('usersByRole', async (ctx, role) => {
  return ctx.bucket('users').where({ role });
});

const admins = await store.runQuery('usersByRole', 'admin');`,
    },
    transactions: {
      filename: 'transactions.ts',
      code: `// Atomic multi-bucket transaction
await store.transaction(async (tx) => {
  const accounts = await tx.bucket('accounts');

  const sender = await accounts.get('alice');
  const receiver = await accounts.get('bob');

  await accounts.update('alice', {
    balance: sender.balance - 100,
  });

  await accounts.update('bob', {
    balance: receiver.balance + 100,
  });

  // Committed on success
  // Rolled back if any operation fails
});

// Read-your-own-writes within transaction
await store.transaction(async (tx) => {
  const users = await tx.bucket('users');

  const user = await users.insert({ name: 'Charlie' });

  // Immediately visible within same transaction
  const found = await users.get(user.id);
  // found.name === 'Charlie'
});`,
    },
  };

  function highlightCode(code: string): string {
    return code
      // Comments first
      .replace(/(\/\/[^\n]*)/g, '<span class="hl-comment">$1</span>')
      // Strings
      .replace(/'([^']*?)'/g, '<span class="hl-string">\'$1\'</span>')
      // Keywords
      .replace(/\b(import|from|export|const|let|return|new|await|async|function|class|extends)\b/g, '<span class="hl-keyword">$1</span>')
      // Classes/Types
      .replace(/\b(Store|BucketHandle)\b/g, '<span class="hl-class">$1</span>')
      // Numbers
      .replace(/\b(\d+)\b/g, '<span class="hl-number">$1</span>')
      // Methods
      .replace(/\b(start|defineBucket|bucket|insert|get|update|delete|where|defineQuery|runQuery|subscribe|transaction|count|findOne)\b(?=\s*[\(\<])/g, '<span class="hl-method">$1</span>')
      // Properties
      .replace(/\b(key|schema|type|generated|required|format|unique|enum|default|name|email|role|status|balance|id|length)\b(?=\s*[:\.])/g, '<span class="hl-property">$1</span>')
      // Arrow functions
      .replace(/=&gt;/g, '<span class="hl-keyword">=&gt;</span>');
  }

  const tabs = $derived([
    { key: 'schema' as const, label: translations.tabs.schema },
    { key: 'queries' as const, label: translations.tabs.queries },
    { key: 'transactions' as const, label: translations.tabs.transactions },
  ]);
</script>

<section
  bind:this={sectionRef}
  id="store-examples"
  class="examples-section snap-section bg-bg-primary"
  aria-labelledby="store-examples-title"
>
  <!-- Background -->
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-blob gradient-blob-1"></div>
    <div class="gradient-blob gradient-blob-2"></div>
    <div class="grid-pattern"></div>
  </div>

  <div class="content-wrapper w-full max-w-4xl mx-auto relative z-10">
    <h2
      id="store-examples-title"
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
      <!-- Tab buttons -->
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

      <!-- Code panel -->
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

  /* Background */
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
    background: var(--color-accent-secondary);
    bottom: -200px;
    right: -200px;
    animation: blobFloat2 18s ease-in-out infinite;
  }

  .grid-pattern {
    position: absolute;
    inset: 0;
    background-image:
      linear-gradient(rgba(0, 212, 255, 0.02) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0, 212, 255, 0.02) 1px, transparent 1px);
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

  /* Title animations */
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

  /* Code container */
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

  /* Tab bar */
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
    color: var(--color-accent-secondary);
    border-bottom-color: var(--color-accent-secondary);
    background: rgba(0, 212, 255, 0.05);
  }

  .tab-button:focus-visible {
    outline: 2px solid var(--color-accent-secondary);
    outline-offset: -2px;
  }

  /* Code panel */
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

  /* Syntax highlighting */
  .code-content :global(.hl-keyword) {
    color: var(--color-accent-secondary);
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

  .code-content :global(.hl-property) {
    color: #9cdcfe;
  }

  .code-content :global(.hl-comment) {
    color: #6a9955;
    font-style: italic;
  }

  /* Responsive */
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

  /* Reduced motion */
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
