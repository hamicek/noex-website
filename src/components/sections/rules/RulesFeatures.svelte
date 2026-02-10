<script lang="ts">
  import { onMount } from 'svelte';

  interface Feature {
    title: string;
    description: string;
  }

  interface RulesFeaturesTranslations {
    title: string;
    subtitle: string;
    forwardChaining: Feature;
    cep: Feature;
    dsl: Feature;
    factStore: Feature;
    timers: Feature;
    api: Feature;
  }

  interface Props {
    translations: RulesFeaturesTranslations;
  }

  let { translations }: Props = $props();

  let sectionRef: HTMLElement | null = $state(null);
  let isVisible = $state(false);
  let cardsVisible = $state([false, false, false, false, false, false]);

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

  const features = $derived([
    {
      key: 'forwardChaining',
      icon: 'engine',
      accent: 'primary',
      data: translations.forwardChaining,
    },
    {
      key: 'cep',
      icon: 'temporal',
      accent: 'secondary',
      data: translations.cep,
    },
    {
      key: 'dsl',
      icon: 'code',
      accent: 'primary',
      data: translations.dsl,
    },
    {
      key: 'factStore',
      icon: 'database',
      accent: 'secondary',
      data: translations.factStore,
    },
    {
      key: 'timers',
      icon: 'clock',
      accent: 'primary',
      data: translations.timers,
    },
    {
      key: 'api',
      icon: 'globe',
      accent: 'secondary',
      data: translations.api,
    },
  ]);
</script>

<section
  bind:this={sectionRef}
  id="rules-features"
  class="features-section snap-section bg-bg-primary"
  aria-labelledby="rules-features-title"
>
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-blob gradient-blob-1"></div>
    <div class="gradient-blob gradient-blob-2"></div>
    <div class="grid-pattern"></div>
  </div>

  <div class="content-wrapper max-w-6xl mx-auto relative z-10">
    <h2
      id="rules-features-title"
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

    <div class="cards-grid grid md:grid-cols-2 lg:grid-cols-3 gap-6">
      {#each features as feature, index}
        <article
          class="feature-card"
          class:visible={cardsVisible[index]}
          class:accent-primary={feature.accent === 'primary'}
          class:accent-secondary={feature.accent === 'secondary'}
          tabindex="0"
        >
          <div class="card-header">
            <div class="card-icon" aria-hidden="true">
              {#if feature.icon === 'engine'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 13.5l10.5-11.25L12 10.5h8.25L9.75 21.75 12 13.5H3.75z" />
                </svg>
              {:else if feature.icon === 'temporal'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M3 13.125C3 12.504 3.504 12 4.125 12h2.25c.621 0 1.125.504 1.125 1.125v6.75C7.5 20.496 6.996 21 6.375 21h-2.25A1.125 1.125 0 013 19.875v-6.75zM9.75 8.625c0-.621.504-1.125 1.125-1.125h2.25c.621 0 1.125.504 1.125 1.125v11.25c0 .621-.504 1.125-1.125 1.125h-2.25a1.125 1.125 0 01-1.125-1.125V8.625zM16.5 4.125c0-.621.504-1.125 1.125-1.125h2.25C20.496 3 21 3.504 21 4.125v15.75c0 .621-.504 1.125-1.125 1.125h-2.25a1.125 1.125 0 01-1.125-1.125V4.125z" />
                </svg>
              {:else if feature.icon === 'code'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M17.25 6.75L22.5 12l-5.25 5.25m-10.5 0L1.5 12l5.25-5.25m7.5-3l-4.5 16.5" />
                </svg>
              {:else if feature.icon === 'database'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M20.25 6.375c0 2.278-3.694 4.125-8.25 4.125S3.75 8.653 3.75 6.375m16.5 0c0-2.278-3.694-4.125-8.25-4.125S3.75 4.097 3.75 6.375m16.5 0v11.25c0 2.278-3.694 4.125-8.25 4.125s-8.25-1.847-8.25-4.125V6.375m16.5 0v3.75m-16.5-3.75v3.75m16.5 0v3.75C20.25 16.153 16.556 18 12 18s-8.25-1.847-8.25-4.125v-3.75m16.5 0c0 2.278-3.694 4.125-8.25 4.125s-8.25-1.847-8.25-4.125" />
                </svg>
              {:else if feature.icon === 'clock'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6h4.5m4.5 0a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
              {:else if feature.icon === 'globe'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M12 21a9.004 9.004 0 008.716-6.747M12 21a9.004 9.004 0 01-8.716-6.747M12 21c2.485 0 4.5-4.03 4.5-9S14.485 3 12 3m0 18c-2.485 0-4.5-4.03-4.5-9S9.515 3 12 3m0 0a8.997 8.997 0 017.843 4.582M12 3a8.997 8.997 0 00-7.843 4.582m15.686 0A11.953 11.953 0 0112 10.5c-2.998 0-5.74-1.1-7.843-2.918m15.686 0A8.959 8.959 0 0121 12c0 .778-.099 1.533-.284 2.253m0 0A17.919 17.919 0 0112 16.5c-3.162 0-6.133-.815-8.716-2.247m0 0A9.015 9.015 0 013 12c0-1.605.42-3.113 1.157-4.418" />
                </svg>
              {/if}
            </div>
            <h3 class="card-title text-xl font-bold">{feature.data.title}</h3>
          </div>

          <p class="card-description text-text-secondary leading-relaxed">
            {feature.data.description}
          </p>

          <div class="card-glow" aria-hidden="true"></div>
        </article>
      {/each}
    </div>
  </div>
</section>

<style>
  .features-section {
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
    opacity: 0.1;
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

  .feature-card {
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

  .feature-card.visible {
    opacity: 1;
    transform: translateY(0) scale(1);
  }

  .feature-card:focus-visible {
    outline: 2px solid var(--color-accent-primary);
    outline-offset: 2px;
  }

  .feature-card.accent-primary:hover {
    border-color: var(--color-accent-primary);
    box-shadow:
      0 0 40px rgba(0, 255, 136, 0.12),
      0 20px 50px rgba(0, 0, 0, 0.3);
    transform: translateY(-4px) scale(1);
  }

  .feature-card.accent-secondary:hover {
    border-color: var(--color-accent-secondary);
    box-shadow:
      0 0 40px rgba(0, 212, 255, 0.12),
      0 20px 50px rgba(0, 0, 0, 0.3);
    transform: translateY(-4px) scale(1);
  }

  .card-header {
    display: flex;
    align-items: center;
    gap: 1rem;
    margin-bottom: 1rem;
  }

  .card-icon {
    width: 44px;
    height: 44px;
    flex-shrink: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 10px;
    transition: transform 0.3s ease, background 0.3s ease;
  }

  .card-icon svg {
    width: 24px;
    height: 24px;
  }

  .accent-primary .card-icon {
    background: linear-gradient(135deg, rgba(0, 255, 136, 0.15), rgba(0, 255, 136, 0.05));
    color: var(--color-accent-primary);
  }

  .accent-secondary .card-icon {
    background: linear-gradient(135deg, rgba(0, 212, 255, 0.15), rgba(0, 212, 255, 0.05));
    color: var(--color-accent-secondary);
  }

  .feature-card:hover .card-icon {
    transform: scale(1.1);
  }

  .card-title {
    color: var(--color-text-primary);
    transition: color 0.3s ease;
  }

  .accent-primary:hover .card-title {
    color: var(--color-accent-primary);
  }

  .accent-secondary:hover .card-title {
    color: var(--color-accent-secondary);
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
    background: linear-gradient(to bottom, rgba(0, 255, 136, 0.08), transparent);
  }

  .accent-secondary .card-glow {
    background: linear-gradient(to bottom, rgba(0, 212, 255, 0.08), transparent);
  }

  .feature-card:hover .card-glow {
    opacity: 1;
  }

  @media (max-width: 1024px) {
    .cards-grid {
      max-width: 600px;
      margin-left: auto;
      margin-right: auto;
    }
  }

  @media (max-width: 768px) {
    .feature-card {
      padding: 1.25rem;
    }

    .card-icon {
      width: 40px;
      height: 40px;
    }

    .card-icon svg {
      width: 22px;
      height: 22px;
    }

    .gradient-blob-1,
    .gradient-blob-2 {
      opacity: 0.06;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .section-title,
    .section-subtitle,
    .feature-card {
      transition: none;
      opacity: 1;
      transform: none;
    }

    .gradient-blob-1,
    .gradient-blob-2 {
      animation: none;
    }

    .feature-card:hover {
      transform: none;
    }

    .feature-card:hover .card-icon {
      transform: none;
    }
  }
</style>
