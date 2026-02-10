<script lang="ts">
  import { onMount } from 'svelte';

  interface FeatureRow {
    label: string;
    noex?: string;
    others?: string[];
    values?: boolean[];
  }

  interface DifferentiatorItem {
    title: string;
    description: string;
  }

  interface ServerComparisonTranslations {
    title: string;
    subtitle: string;
    noexServer: string;
    competitors: string[];
    features: FeatureRow[];
    differentiators: {
      title: string;
      items: DifferentiatorItem[];
    };
  }

  interface Props {
    translations: ServerComparisonTranslations;
  }

  let { translations }: Props = $props();

  let sectionRef: HTMLElement | null = $state(null);
  let isVisible = $state(false);

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

  function cellValue(row: FeatureRow, index: number): string {
    if (row.values) {
      return row.values[index] ? '\u2713' : '\u2014';
    }
    if (index === 0) return row.noex ?? '\u2014';
    return row.others?.[index - 1] ?? '\u2014';
  }

  function isCheck(row: FeatureRow, index: number): boolean {
    if (row.values) return row.values[index] === true;
    return false;
  }

  function isDash(value: string): boolean {
    return value === '\u2014';
  }
</script>

<section
  bind:this={sectionRef}
  id="server-comparison"
  class="comparison-section snap-section bg-bg-primary"
  aria-labelledby="server-comparison-title"
>
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-blob gradient-blob-1"></div>
    <div class="gradient-blob gradient-blob-2"></div>
    <div class="grid-pattern"></div>
  </div>

  <div class="content-wrapper w-full max-w-6xl mx-auto relative z-10">
    <h2
      id="server-comparison-title"
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

    <div class="table-wrapper glass-card" class:visible={isVisible}>
      <div class="table-scroll" tabindex="0" role="region" aria-label="Scrollable comparison table">
        <table>
          <thead>
            <tr>
              <th class="feature-col">Feature</th>
              <th class="noex-col">{translations.noexServer}</th>
              {#each translations.competitors as competitor}
                <th>{competitor}</th>
              {/each}
            </tr>
          </thead>
          <tbody>
            {#each translations.features as row, rowIndex}
              <tr class:alt-row={rowIndex % 2 === 1}>
                <td class="feature-col">{row.label}</td>
                <td class="noex-col" class:check-cell={isCheck(row, 0)} class:dash-cell={isDash(cellValue(row, 0))}>
                  {cellValue(row, 0)}
                </td>
                {#each translations.competitors as _, i}
                  <td class:check-cell={isCheck(row, i + 1)} class:dash-cell={isDash(cellValue(row, i + 1))}>
                    {cellValue(row, i + 1)}
                  </td>
                {/each}
              </tr>
            {/each}
          </tbody>
        </table>
      </div>
    </div>

    <h3
      class="diff-title text-2xl md:text-3xl font-bold mt-16 mb-8 text-text-primary text-center"
      class:visible={isVisible}
    >
      {translations.differentiators.title}
    </h3>

    <div class="differentiators-grid" class:visible={isVisible}>
      {#each translations.differentiators.items as item, i}
        <article class="diff-card" style="--delay: {0.1 * i}s">
          <h4 class="diff-card-title">{item.title}</h4>
          <p class="diff-card-desc">{item.description}</p>
        </article>
      {/each}
    </div>
  </div>
</section>

<style>
  .comparison-section {
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
    background: #a78bfa;
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

  .table-wrapper {
    position: relative;
  }

  .table-scroll {
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    min-width: 800px;
    font-size: 0.85rem;
  }

  thead {
    position: sticky;
    top: 0;
    z-index: 2;
  }

  th {
    padding: 0.875rem 1rem;
    text-align: center;
    font-weight: 600;
    color: var(--color-text-primary);
    background: rgba(26, 26, 46, 0.95);
    backdrop-filter: blur(8px);
    border-bottom: 1px solid var(--color-border);
    white-space: nowrap;
  }

  td {
    padding: 0.75rem 1rem;
    text-align: center;
    color: var(--color-text-secondary);
    border-bottom: 1px solid rgba(255, 255, 255, 0.04);
    white-space: nowrap;
  }

  .feature-col {
    text-align: left;
    position: sticky;
    left: 0;
    z-index: 1;
    background: rgba(18, 18, 26, 0.95);
    backdrop-filter: blur(8px);
    font-weight: 500;
    color: var(--color-text-primary);
    min-width: 180px;
  }

  thead .feature-col {
    z-index: 3;
  }

  .noex-col {
    background: rgba(167, 139, 250, 0.05);
    color: #a78bfa;
    font-weight: 500;
  }

  thead .noex-col {
    background: rgba(167, 139, 250, 0.1);
    color: #a78bfa;
  }

  .alt-row td {
    background-color: rgba(255, 255, 255, 0.015);
  }

  .alt-row .feature-col {
    background: rgba(22, 22, 34, 0.95);
  }

  .alt-row .noex-col {
    background: rgba(167, 139, 250, 0.065);
  }

  .check-cell {
    color: var(--color-accent-primary);
    font-size: 1.1rem;
  }

  .dash-cell {
    opacity: 0.4;
  }

  .diff-title {
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out 0.35s, transform 0.6s ease-out 0.35s;
  }

  .diff-title.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .differentiators-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 1.25rem;
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out 0.45s, transform 0.6s ease-out 0.45s;
  }

  .differentiators-grid.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .diff-card {
    background: rgba(26, 26, 46, 0.6);
    border: 1px solid var(--color-border);
    border-radius: 12px;
    padding: 1.5rem;
    backdrop-filter: blur(8px);
    transition: border-color 0.2s ease, transform 0.2s ease;
  }

  .diff-card:hover {
    border-color: rgba(167, 139, 250, 0.3);
    transform: translateY(-2px);
  }

  .diff-card-title {
    font-size: 1rem;
    font-weight: 600;
    color: #a78bfa;
    margin-bottom: 0.5rem;
  }

  .diff-card-desc {
    font-size: 0.875rem;
    line-height: 1.6;
    color: var(--color-text-secondary);
  }

  @media (max-width: 768px) {
    table {
      font-size: 0.75rem;
      min-width: 640px;
    }

    th, td {
      padding: 0.5rem 0.625rem;
    }

    .feature-col {
      min-width: 120px;
    }

    .table-scroll {
      position: relative;
    }

    /* Scroll fade hint on right edge */
    .table-wrapper::after {
      content: '';
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      width: 24px;
      background: linear-gradient(to right, transparent, rgba(18, 18, 26, 0.9));
      pointer-events: none;
      z-index: 4;
      border-radius: 0 16px 16px 0;
    }

    .differentiators-grid {
      grid-template-columns: 1fr;
    }

    .diff-card {
      padding: 1.25rem;
    }

    .gradient-blob-1,
    .gradient-blob-2 {
      opacity: 0.05;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .section-title,
    .section-subtitle,
    .glass-card,
    .diff-title,
    .differentiators-grid {
      transition: none;
      opacity: 1;
      transform: none;
    }

    .gradient-blob-1,
    .gradient-blob-2 {
      animation: none;
    }

    .diff-card {
      transition: none;
    }
  }
</style>
