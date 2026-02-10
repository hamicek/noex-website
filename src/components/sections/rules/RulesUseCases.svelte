<script lang="ts">
  import { onMount } from 'svelte';

  interface UseCase {
    title: string;
    description: string;
  }

  interface RulesUseCasesTranslations {
    title: string;
    subtitle: string;
    fraud: UseCase;
    payment: UseCase;
    loyalty: UseCase;
    iot: UseCase;
  }

  interface Props {
    translations: RulesUseCasesTranslations;
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
              }, 150 * (index + 1));
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
    fraud: `Rule.create('brute-force-detection')
  .when(count()
    .event('auth.login_failed')
    .threshold(5)
    .window('5m')
    .groupBy('userId'))
  .then(emit('security.alert', {
    type: 'brute-force',
    userId: ref('trigger.groupKey'),
  }))
  .build();`,
    payment: `Rule.create('payment-timeout')
  .when(absence()
    .after('order.created')
    .expected('payment.received')
    .within('15m')
    .groupBy('orderId'))
  .then(emit('order.cancel', {
    orderId: ref('trigger.groupKey'),
    reason: 'Payment timeout',
  }))
  .build();`,
    loyalty: `Rule.create('gold-tier-upgrade')
  .when(aggregate()
    .event('order.paid')
    .field('amount')
    .function('sum')
    .threshold(10000)
    .window('30d')
    .groupBy('customerId'))
  .then(setFact(
    'customer:\${trigger.groupKey}:tier', 'gold'
  ))
  .build();`,
    iot: `Rule.create('device-overheat')
  .when(sequence()
    .event('sensor.temp_high')
    .event('sensor.temp_critical')
    .within('10m')
    .groupBy('deviceId'))
  .then(emit('alert.overheat', {
    deviceId: ref('trigger.groupKey'),
    severity: 'critical',
  }))
  .build();`,
  };

  const useCases = $derived([
    { key: 'fraud', icon: 'shield', accent: 'secondary', data: translations.fraud },
    { key: 'payment', icon: 'credit', accent: 'primary', data: translations.payment },
    { key: 'loyalty', icon: 'star', accent: 'secondary', data: translations.loyalty },
    { key: 'iot', icon: 'cpu', accent: 'primary', data: translations.iot },
  ]);
</script>

<section
  bind:this={sectionRef}
  id="rules-usecases"
  class="usecases-section snap-section bg-bg-secondary"
  aria-labelledby="rules-usecases-title"
>
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-blob gradient-blob-1"></div>
    <div class="gradient-blob gradient-blob-2"></div>
    <div class="dot-pattern"></div>
  </div>

  <div class="content-wrapper max-w-6xl mx-auto relative z-10">
    <h2
      id="rules-usecases-title"
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

    <div class="usecases-grid grid md:grid-cols-2 gap-6">
      {#each useCases as useCase, index}
        <article
          class="usecase-card"
          class:visible={cardsVisible[index]}
          class:accent-primary={useCase.accent === 'primary'}
          class:accent-secondary={useCase.accent === 'secondary'}
          tabindex="0"
        >
          <div class="card-header">
            <div class="card-icon" aria-hidden="true">
              {#if useCase.icon === 'shield'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75L11.25 15 15 9.75m-3-7.036A11.959 11.959 0 013.598 6 11.99 11.99 0 003 9.749c0 5.592 3.824 10.29 9 11.623 5.176-1.332 9-6.03 9-11.622 0-1.31-.21-2.571-.598-3.751h-.152c-3.196 0-6.1-1.248-8.25-3.285z" />
                </svg>
              {:else if useCase.icon === 'credit'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M2.25 8.25h19.5M2.25 9h19.5m-16.5 5.25h6m-6 2.25h3m-3.75 3h15a2.25 2.25 0 002.25-2.25V6.75A2.25 2.25 0 0019.5 4.5h-15a2.25 2.25 0 00-2.25 2.25v10.5A2.25 2.25 0 004.5 19.5z" />
                </svg>
              {:else if useCase.icon === 'star'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M11.48 3.499a.562.562 0 011.04 0l2.125 5.111a.563.563 0 00.475.345l5.518.442c.499.04.701.663.321.988l-4.204 3.602a.563.563 0 00-.182.557l1.285 5.385a.562.562 0 01-.84.61l-4.725-2.885a.563.563 0 00-.586 0L6.982 20.54a.562.562 0 01-.84-.61l1.285-5.386a.562.562 0 00-.182-.557l-4.204-3.602a.563.563 0 01.321-.988l5.518-.442a.563.563 0 00.475-.345L11.48 3.5z" />
                </svg>
              {:else if useCase.icon === 'cpu'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M8.25 3v1.5M4.5 8.25H3m18 0h-1.5M4.5 12H3m18 0h-1.5m-15 3.75H3m18 0h-1.5M8.25 19.5V21M12 3v1.5m0 15V21m3.75-18v1.5m0 15V21m-9-1.5h10.5a2.25 2.25 0 002.25-2.25V6.75a2.25 2.25 0 00-2.25-2.25H6.75A2.25 2.25 0 004.5 6.75v10.5a2.25 2.25 0 002.25 2.25zm.75-12h9v9h-9v-9z" />
                </svg>
              {/if}
            </div>
            <div>
              <h3 class="card-title text-xl font-bold text-text-primary">{useCase.data.title}</h3>
              <p class="card-description text-text-secondary text-sm mt-1">{useCase.data.description}</p>
            </div>
          </div>

          <div class="code-snippet">
            <pre><code>{codeSnippets[useCase.key]}</code></pre>
          </div>

          <div class="card-glow" aria-hidden="true"></div>
        </article>
      {/each}
    </div>
  </div>
</section>

<style>
  .usecases-section {
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
    top: -150px;
    left: -150px;
    animation: blobFloat1 20s ease-in-out infinite;
  }

  .gradient-blob-2 {
    width: 400px;
    height: 400px;
    background: var(--color-accent-primary);
    bottom: -100px;
    right: -100px;
    animation: blobFloat2 18s ease-in-out infinite;
  }

  .dot-pattern {
    position: absolute;
    inset: 0;
    background-image: radial-gradient(rgba(0, 212, 255, 0.06) 1px, transparent 1px);
    background-size: 24px 24px;
  }

  @keyframes blobFloat1 {
    0%, 100% { transform: translate(0, 0); }
    50% { transform: translate(50px, 30px); }
  }

  @keyframes blobFloat2 {
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

  .usecase-card {
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

  .usecase-card.visible {
    opacity: 1;
    transform: translateY(0) scale(1);
  }

  .usecase-card:focus-visible {
    outline: 2px solid var(--color-accent-primary);
    outline-offset: 2px;
  }

  .usecase-card.accent-primary:hover {
    border-color: var(--color-accent-primary);
    box-shadow:
      0 0 40px rgba(0, 255, 136, 0.1),
      0 20px 50px rgba(0, 0, 0, 0.3);
    transform: translateY(-4px) scale(1);
  }

  .usecase-card.accent-secondary:hover {
    border-color: var(--color-accent-secondary);
    box-shadow:
      0 0 40px rgba(0, 212, 255, 0.1),
      0 20px 50px rgba(0, 0, 0, 0.3);
    transform: translateY(-4px) scale(1);
  }

  .card-header {
    display: flex;
    align-items: flex-start;
    gap: 1rem;
    margin-bottom: 1rem;
  }

  .card-icon {
    width: 40px;
    height: 40px;
    flex-shrink: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 10px;
    transition: transform 0.3s ease;
  }

  .card-icon svg {
    width: 22px;
    height: 22px;
  }

  .accent-primary .card-icon {
    background: linear-gradient(135deg, rgba(0, 255, 136, 0.15), rgba(0, 255, 136, 0.05));
    color: var(--color-accent-primary);
  }

  .accent-secondary .card-icon {
    background: linear-gradient(135deg, rgba(0, 212, 255, 0.15), rgba(0, 212, 255, 0.05));
    color: var(--color-accent-secondary);
  }

  .usecase-card:hover .card-icon {
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
    font-size: 0.72rem;
    line-height: 1.6;
    color: var(--color-text-secondary);
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
    opacity: 0;
    transition: opacity 0.3s ease;
    pointer-events: none;
  }

  .accent-primary .card-glow {
    background: linear-gradient(to bottom, rgba(0, 255, 136, 0.06), transparent);
  }

  .accent-secondary .card-glow {
    background: linear-gradient(to bottom, rgba(0, 212, 255, 0.06), transparent);
  }

  .usecase-card:hover .card-glow {
    opacity: 1;
  }

  @media (max-width: 768px) {
    .usecase-card {
      padding: 1.25rem;
    }

    .code-snippet pre {
      font-size: 0.65rem;
    }

    .gradient-blob-1,
    .gradient-blob-2 {
      opacity: 0.05;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .section-title,
    .section-subtitle,
    .usecase-card {
      transition: none;
      opacity: 1;
      transform: none;
    }

    .gradient-blob-1,
    .gradient-blob-2 {
      animation: none;
    }

    .usecase-card:hover {
      transform: none;
    }

    .usecase-card:hover .card-icon {
      transform: none;
    }
  }
</style>
