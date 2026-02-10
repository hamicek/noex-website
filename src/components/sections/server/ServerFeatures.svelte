<script lang="ts">
  import { onMount } from 'svelte';

  interface Feature {
    title: string;
    description: string;
  }

  interface ServerFeaturesTranslations {
    title: string;
    subtitle: string;
    genserver: Feature;
    storeProxy: Feature;
    rulesProxy: Feature;
    auth: Feature;
    rateLimit: Feature;
    heartbeat: Feature;
    backpressure: Feature;
    gracefulShutdown: Feature;
  }

  interface Props {
    translations: ServerFeaturesTranslations;
  }

  let { translations }: Props = $props();

  let sectionRef: HTMLElement | null = $state(null);
  let isVisible = $state(false);
  let cardsVisible = $state([false, false, false, false, false, false, false, false]);

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
    { key: 'genserver', accent: 'violet', data: translations.genserver },
    { key: 'storeProxy', accent: 'green', data: translations.storeProxy },
    { key: 'rulesProxy', accent: 'violet', data: translations.rulesProxy },
    { key: 'auth', accent: 'green', data: translations.auth },
    { key: 'rateLimit', accent: 'violet', data: translations.rateLimit },
    { key: 'heartbeat', accent: 'green', data: translations.heartbeat },
    { key: 'backpressure', accent: 'violet', data: translations.backpressure },
    { key: 'gracefulShutdown', accent: 'green', data: translations.gracefulShutdown },
  ]);
</script>

<section
  bind:this={sectionRef}
  id="server-features"
  class="features-section snap-section bg-bg-primary"
  aria-labelledby="server-features-title"
>
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-blob gradient-blob-1"></div>
    <div class="gradient-blob gradient-blob-2"></div>
    <div class="grid-pattern"></div>
  </div>

  <div class="content-wrapper max-w-6xl mx-auto relative z-10">
    <h2
      id="server-features-title"
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

    <div class="cards-grid grid md:grid-cols-2 lg:grid-cols-4 gap-6">
      {#each features as feature, index}
        <article
          class="feature-card"
          class:visible={cardsVisible[index]}
          class:accent-violet={feature.accent === 'violet'}
          class:accent-green={feature.accent === 'green'}
          tabindex="0"
        >
          <div class="card-header">
            <div class="card-icon" aria-hidden="true">
              {#if feature.key === 'genserver'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M8.25 3v1.5M4.5 8.25H3m18 0h-1.5M4.5 12H3m18 0h-1.5m-15 3.75H3m18 0h-1.5M8.25 19.5V21M12 3v1.5m0 15V21m3.75-18v1.5m0 15V21m-9-1.5h10.5a2.25 2.25 0 002.25-2.25V6.75a2.25 2.25 0 00-2.25-2.25H6.75A2.25 2.25 0 004.5 6.75v10.5a2.25 2.25 0 002.25 2.25z" />
                </svg>
              {:else if feature.key === 'storeProxy'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M20.25 6.375c0 2.278-3.694 4.125-8.25 4.125S3.75 8.653 3.75 6.375m16.5 0c0-2.278-3.694-4.125-8.25-4.125S3.75 4.097 3.75 6.375m16.5 0v11.25c0 2.278-3.694 4.125-8.25 4.125s-8.25-1.847-8.25-4.125V6.375m16.5 0v3.75m-16.5-3.75v3.75m16.5 0v3.75C20.25 16.153 16.556 18 12 18s-8.25-1.847-8.25-4.125v-3.75m16.5 0c0 2.278-3.694 4.125-8.25 4.125s-8.25-1.847-8.25-4.125" />
                </svg>
              {:else if feature.key === 'rulesProxy'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 13.5l10.5-11.25L12 10.5h8.25L9.75 21.75 12 13.5H3.75z" />
                </svg>
              {:else if feature.key === 'auth'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75L11.25 15 15 9.75m-3-7.036A11.959 11.959 0 013.598 6 11.99 11.99 0 003 9.749c0 5.592 3.824 10.29 9 11.623 5.176-1.332 9-6.03 9-11.622 0-1.31-.21-2.571-.598-3.751h-.152c-3.196 0-6.1-1.248-8.25-3.285z" />
                </svg>
              {:else if feature.key === 'rateLimit'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6h4.5m4.5 0a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
              {:else if feature.key === 'heartbeat'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M21 8.25c0-2.485-2.099-4.5-4.688-4.5-1.935 0-3.597 1.126-4.312 2.733-.715-1.607-2.377-2.733-4.313-2.733C5.1 3.75 3 5.765 3 8.25c0 7.22 9 12 9 12s9-4.78 9-12z" />
                </svg>
              {:else if feature.key === 'backpressure'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M3 4.5h14.25M3 9h9.75M3 13.5h9.75m4.5-4.5v12m0 0l-3.75-3.75M17.25 21L21 17.25" />
                </svg>
              {:else if feature.key === 'gracefulShutdown'}
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M5.636 5.636a9 9 0 1012.728 0M12 3v9" />
                </svg>
              {/if}
            </div>
            <h3 class="card-title text-lg font-bold">{feature.data.title}</h3>
          </div>

          <p class="card-description text-text-secondary text-sm leading-relaxed">
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
    background: #a78bfa;
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

  .feature-card {
    position: relative;
    background: rgba(26, 26, 46, 0.6);
    backdrop-filter: blur(12px);
    border: 1px solid var(--color-border);
    border-radius: 1rem;
    padding: 1.25rem;
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
    outline: 2px solid #a78bfa;
    outline-offset: 2px;
  }

  .feature-card.accent-violet:hover {
    border-color: #a78bfa;
    box-shadow:
      0 0 40px rgba(167, 139, 250, 0.12),
      0 20px 50px rgba(0, 0, 0, 0.3);
    transform: translateY(-4px) scale(1);
  }

  .feature-card.accent-green:hover {
    border-color: var(--color-accent-primary);
    box-shadow:
      0 0 40px rgba(0, 255, 136, 0.12),
      0 20px 50px rgba(0, 0, 0, 0.3);
    transform: translateY(-4px) scale(1);
  }

  .card-header {
    display: flex;
    align-items: center;
    gap: 0.75rem;
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
    transition: transform 0.3s ease;
  }

  .card-icon svg {
    width: 22px;
    height: 22px;
  }

  .accent-violet .card-icon {
    background: linear-gradient(135deg, rgba(167, 139, 250, 0.15), rgba(167, 139, 250, 0.05));
    color: #a78bfa;
  }

  .accent-green .card-icon {
    background: linear-gradient(135deg, rgba(0, 255, 136, 0.15), rgba(0, 255, 136, 0.05));
    color: var(--color-accent-primary);
  }

  .feature-card:hover .card-icon {
    transform: scale(1.1);
  }

  .card-title {
    color: var(--color-text-primary);
    transition: color 0.3s ease;
  }

  .accent-violet:hover .card-title {
    color: #a78bfa;
  }

  .accent-green:hover .card-title {
    color: var(--color-accent-primary);
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

  .accent-violet .card-glow {
    background: linear-gradient(to bottom, rgba(167, 139, 250, 0.08), transparent);
  }

  .accent-green .card-glow {
    background: linear-gradient(to bottom, rgba(0, 255, 136, 0.08), transparent);
  }

  .feature-card:hover .card-glow {
    opacity: 1;
  }

  @media (max-width: 1024px) {
    .cards-grid {
      max-width: 700px;
      margin-left: auto;
      margin-right: auto;
      grid-template-columns: repeat(2, 1fr) !important;
    }
  }

  @media (max-width: 768px) {
    .cards-grid {
      grid-template-columns: 1fr !important;
      max-width: 500px;
    }

    .feature-card {
      padding: 1.25rem;
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
