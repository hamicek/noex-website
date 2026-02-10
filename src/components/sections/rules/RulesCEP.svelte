<script lang="ts">
  import { onMount } from 'svelte';

  interface CEPPattern {
    title: string;
    description: string;
  }

  interface RulesCEPTranslations {
    title: string;
    subtitle: string;
    sequence: CEPPattern;
    absence: CEPPattern;
    count: CEPPattern;
    aggregate: CEPPattern;
  }

  interface Props {
    translations: RulesCEPTranslations;
  }

  let { translations }: Props = $props();

  let sectionRef: HTMLElement | null = $state(null);
  let isVisible = $state(false);
  let cardsVisible = $state([false, false, false, false]);

  onMount(() => {
    if (!sectionRef) return;

    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            isVisible = true;
            cardsVisible.forEach((_, index) => {
              setTimeout(() => {
                cardsVisible[index] = true;
              }, 200 * (index + 1));
            });
          }
        });
      },
      { threshold: 0.1 }
    );

    observer.observe(sectionRef);
    return () => observer.disconnect();
  });

  const codeSnippets: Record<string, string> = {
    sequence: `sequence()
  .event('order.created')
  .event('payment.received')
  .within('15m')
  .groupBy('orderId')`,
    absence: `absence()
  .after('order.created')
  .expected('payment.received')
  .within('15m')
  .groupBy('orderId')`,
    count: `count()
  .event('auth.login_failed')
  .threshold(5)
  .window('5m')
  .groupBy('userId')`,
    aggregate: `aggregate()
  .event('order.paid')
  .field('amount')
  .function('sum')
  .threshold(10000)
  .window('1h')`,
  };

  const patterns = $derived([
    { key: 'sequence', icon: 'sequence', data: translations.sequence },
    { key: 'absence', icon: 'absence', data: translations.absence },
    { key: 'count', icon: 'count', data: translations.count },
    { key: 'aggregate', icon: 'aggregate', data: translations.aggregate },
  ]);
</script>

<section
  bind:this={sectionRef}
  id="rules-cep"
  class="cep-section snap-section bg-bg-primary"
  aria-labelledby="rules-cep-title"
>
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-blob gradient-blob-1"></div>
    <div class="gradient-blob gradient-blob-2"></div>
    <div class="grid-pattern"></div>
  </div>

  <div class="content-wrapper max-w-6xl mx-auto relative z-10">
    <h2
      id="rules-cep-title"
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

    <div class="patterns-grid grid md:grid-cols-2 gap-6">
      {#each patterns as pattern, index}
        <article
          class="pattern-card"
          class:visible={cardsVisible[index]}
          tabindex="0"
        >
          <div class="card-header">
            <div class="card-icon" aria-hidden="true">
              {#if pattern.icon === 'sequence'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M13.5 4.5L21 12m0 0l-7.5 7.5M21 12H3" />
                </svg>
              {:else if pattern.icon === 'absence'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M12 9v3.75m9-.75a9 9 0 11-18 0 9 9 0 0118 0zm-9 3.75h.008v.008H12v-.008z" />
                </svg>
              {:else if pattern.icon === 'count'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M3 13.125C3 12.504 3.504 12 4.125 12h2.25c.621 0 1.125.504 1.125 1.125v6.75C7.5 20.496 6.996 21 6.375 21h-2.25A1.125 1.125 0 013 19.875v-6.75zM9.75 8.625c0-.621.504-1.125 1.125-1.125h2.25c.621 0 1.125.504 1.125 1.125v11.25c0 .621-.504 1.125-1.125 1.125h-2.25a1.125 1.125 0 01-1.125-1.125V8.625zM16.5 4.125c0-.621.504-1.125 1.125-1.125h2.25C20.496 3 21 3.504 21 4.125v15.75c0 .621-.504 1.125-1.125 1.125h-2.25a1.125 1.125 0 01-1.125-1.125V4.125z" />
                </svg>
              {:else if pattern.icon === 'aggregate'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 15.75V18m-7.5-6.75V18m15-8.25v.015M3 18.75V7.5a2.25 2.25 0 012.25-2.25h13.5A2.25 2.25 0 0121 7.5v11.25m-18 0A2.25 2.25 0 005.25 21h13.5A2.25 2.25 0 0021 18.75m-18 0v-7.5A2.25 2.25 0 015.25 9h13.5A2.25 2.25 0 0121 11.25v7.5" />
                </svg>
              {/if}
            </div>
            <h3 class="card-title text-xl font-bold text-text-primary">{pattern.data.title}</h3>
          </div>

          <p class="card-description text-text-secondary leading-relaxed mb-4">
            {pattern.data.description}
          </p>

          <div class="code-snippet">
            <pre><code>{codeSnippets[pattern.key]}</code></pre>
          </div>

          <div class="card-glow" aria-hidden="true"></div>
        </article>
      {/each}
    </div>
  </div>
</section>

<style>
  .cep-section {
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
    right: -200px;
    animation: blobFloat1 15s ease-in-out infinite;
  }

  .gradient-blob-2 {
    width: 400px;
    height: 400px;
    background: var(--color-accent-secondary);
    bottom: -200px;
    left: -200px;
    animation: blobFloat2 18s ease-in-out infinite;
  }

  .grid-pattern {
    position: absolute;
    inset: 0;
    background-image:
      linear-gradient(rgba(0, 255, 136, 0.02) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0, 255, 136, 0.02) 1px, transparent 1px);
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

  .pattern-card {
    position: relative;
    background: rgba(26, 26, 46, 0.6);
    backdrop-filter: blur(12px);
    border: 1px solid var(--color-border);
    border-radius: 1rem;
    padding: 1.5rem;
    opacity: 0;
    transform: translateY(40px) scale(0.95);
    transition:
      opacity 0.5s ease-out,
      transform 0.5s ease-out,
      border-color 0.3s ease,
      box-shadow 0.3s ease;
    overflow: hidden;
    cursor: default;
    outline: none;
  }

  .pattern-card.visible {
    opacity: 1;
    transform: translateY(0) scale(1);
  }

  .pattern-card:focus-visible {
    outline: 2px solid var(--color-accent-primary);
    outline-offset: 2px;
  }

  .pattern-card:hover {
    border-color: var(--color-accent-primary);
    box-shadow:
      0 0 40px rgba(0, 255, 136, 0.1),
      0 20px 50px rgba(0, 0, 0, 0.3);
    transform: translateY(-4px) scale(1);
  }

  .card-header {
    display: flex;
    align-items: center;
    gap: 1rem;
    margin-bottom: 0.75rem;
  }

  .card-icon {
    width: 40px;
    height: 40px;
    flex-shrink: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 10px;
    background: linear-gradient(135deg, rgba(0, 255, 136, 0.15), rgba(0, 255, 136, 0.05));
    color: var(--color-accent-primary);
    transition: transform 0.3s ease;
  }

  .card-icon svg {
    width: 22px;
    height: 22px;
  }

  .pattern-card:hover .card-icon {
    transform: scale(1.1);
  }

  .code-snippet {
    background: rgba(18, 18, 26, 0.8);
    border: 1px solid var(--color-border);
    border-radius: 8px;
    padding: 0.75rem 1rem;
    overflow-x: auto;
  }

  .code-snippet pre {
    margin: 0;
    font-family: 'JetBrains Mono', 'Fira Code', monospace;
    font-size: 0.75rem;
    line-height: 1.6;
    color: var(--color-accent-primary);
  }

  .code-snippet code {
    display: block;
    white-space: pre;
  }

  .card-glow {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    height: 80px;
    background: linear-gradient(to bottom, rgba(0, 255, 136, 0.06), transparent);
    opacity: 0;
    transition: opacity 0.3s ease;
    pointer-events: none;
  }

  .pattern-card:hover .card-glow {
    opacity: 1;
  }

  @media (max-width: 768px) {
    .pattern-card {
      padding: 1.25rem;
    }

    .code-snippet pre {
      font-size: 0.68rem;
    }

    .gradient-blob-1,
    .gradient-blob-2 {
      opacity: 0.05;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .section-title,
    .section-subtitle,
    .pattern-card {
      transition: none;
      opacity: 1;
      transform: none;
    }

    .gradient-blob-1,
    .gradient-blob-2 {
      animation: none;
    }

    .pattern-card:hover {
      transform: none;
    }

    .pattern-card:hover .card-icon {
      transform: none;
    }
  }
</style>
