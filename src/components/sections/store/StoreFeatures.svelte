<script lang="ts">
  import { onMount } from 'svelte';

  interface Feature {
    title: string;
    description: string;
  }

  interface StoreFeaturesTranslations {
    title: string;
    subtitle: string;
    schema: Feature;
    reactiveQueries: Feature;
    transactions: Feature;
    persistence: Feature;
    ttl: Feature;
    events: Feature;
  }

  interface Props {
    translations: StoreFeaturesTranslations;
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
      key: 'schema',
      icon: 'schema',
      accent: 'secondary',
      data: translations.schema,
    },
    {
      key: 'reactiveQueries',
      icon: 'reactive',
      accent: 'primary',
      data: translations.reactiveQueries,
    },
    {
      key: 'transactions',
      icon: 'lock',
      accent: 'secondary',
      data: translations.transactions,
    },
    {
      key: 'persistence',
      icon: 'disk',
      accent: 'primary',
      data: translations.persistence,
    },
    {
      key: 'ttl',
      icon: 'clock',
      accent: 'secondary',
      data: translations.ttl,
    },
    {
      key: 'events',
      icon: 'broadcast',
      accent: 'primary',
      data: translations.events,
    },
  ]);
</script>

<section
  bind:this={sectionRef}
  id="store-features"
  class="features-section snap-section bg-bg-primary"
  aria-labelledby="store-features-title"
>
  <!-- Background elements -->
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-blob gradient-blob-1"></div>
    <div class="gradient-blob gradient-blob-2"></div>
    <div class="grid-pattern"></div>
  </div>

  <div class="content-wrapper max-w-6xl mx-auto relative z-10">
    <h2
      id="store-features-title"
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
              {#if feature.icon === 'schema'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 6A2.25 2.25 0 016 3.75h2.25A2.25 2.25 0 0110.5 6v2.25a2.25 2.25 0 01-2.25 2.25H6a2.25 2.25 0 01-2.25-2.25V6zM3.75 15.75A2.25 2.25 0 016 13.5h2.25a2.25 2.25 0 012.25 2.25V18a2.25 2.25 0 01-2.25 2.25H6A2.25 2.25 0 013.75 18v-2.25zM13.5 6a2.25 2.25 0 012.25-2.25H18A2.25 2.25 0 0120.25 6v2.25A2.25 2.25 0 0118 10.5h-2.25a2.25 2.25 0 01-2.25-2.25V6zM13.5 15.75a2.25 2.25 0 012.25-2.25H18a2.25 2.25 0 012.25 2.25V18A2.25 2.25 0 0118 20.25h-2.25A2.25 2.25 0 0113.5 18v-2.25z" />
                </svg>
              {:else if feature.icon === 'reactive'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M19.5 12c0-1.232-.046-2.453-.138-3.662a4.006 4.006 0 00-3.7-3.7 48.678 48.678 0 00-7.324 0 4.006 4.006 0 00-3.7 3.7c-.017.22-.032.441-.046.662M19.5 12l3-3m-3 3l-3-3m-12 3c0 1.232.046 2.453.138 3.662a4.006 4.006 0 003.7 3.7 48.656 48.656 0 007.324 0 4.006 4.006 0 003.7-3.7c.017-.22.032-.441.046-.662M4.5 12l3 3m-3-3l-3 3" />
                </svg>
              {:else if feature.icon === 'lock'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M16.5 10.5V6.75a4.5 4.5 0 10-9 0v3.75m-.75 11.25h10.5a2.25 2.25 0 002.25-2.25v-6.75a2.25 2.25 0 00-2.25-2.25H6.75a2.25 2.25 0 00-2.25 2.25v6.75a2.25 2.25 0 002.25 2.25z" />
                </svg>
              {:else if feature.icon === 'disk'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M20.25 6.375c0 2.278-3.694 4.125-8.25 4.125S3.75 8.653 3.75 6.375m16.5 0c0-2.278-3.694-4.125-8.25-4.125S3.75 4.097 3.75 6.375m16.5 0v11.25c0 2.278-3.694 4.125-8.25 4.125s-8.25-1.847-8.25-4.125V6.375m16.5 0v3.75m-16.5-3.75v3.75m16.5 0v3.75C20.25 16.153 16.556 18 12 18s-8.25-1.847-8.25-4.125v-3.75m16.5 0c0 2.278-3.694 4.125-8.25 4.125s-8.25-1.847-8.25-4.125" />
                </svg>
              {:else if feature.icon === 'clock'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6h4.5m4.5 0a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
              {:else if feature.icon === 'broadcast'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path d="M4.9 19.1C1 15.2 1 8.8 4.9 4.9" />
                  <path d="M7.8 16.2a5.5 5.5 0 0 1 0-8.4" />
                  <circle cx="12" cy="12" r="2" />
                  <path d="M16.2 7.8a5.5 5.5 0 0 1 0 8.4" />
                  <path d="M19.1 4.9C23 8.8 23 15.2 19.1 19.1" />
                </svg>
              {/if}
            </div>
            <h3 class="card-title text-xl font-bold">{feature.data.title}</h3>
          </div>

          <p class="card-description text-text-secondary leading-relaxed">
            {feature.data.description}
          </p>

          <!-- Hover glow -->
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
    opacity: 0.1;
  }

  .gradient-blob-1 {
    width: 500px;
    height: 500px;
    background: var(--color-accent-secondary);
    top: -200px;
    right: -200px;
    animation: blobFloat1 15s ease-in-out infinite;
  }

  .gradient-blob-2 {
    width: 400px;
    height: 400px;
    background: var(--color-accent-primary);
    bottom: -200px;
    left: -200px;
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

  /* Feature cards */
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
    outline: 2px solid var(--color-accent-secondary);
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

  /* Card header */
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

  /* Card title */
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

  /* Card glow */
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

  /* Responsive */
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

  /* Reduced motion */
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
