<script lang="ts">
  import { onMount } from 'svelte';

  interface StoreArchitectureTranslations {
    title: string;
    subtitle: string;
    store: string;
    supervisor: string;
    bucketServer: string;
    queryManager: string;
    eventBus: string;
    ttlManager: string;
    persistence: string;
    bucketIsolation: string;
    reactiveLayer: string;
  }

  interface Props {
    translations: StoreArchitectureTranslations;
  }

  let { translations }: Props = $props();

  let sectionRef: HTMLElement | null = $state(null);
  let isVisible = $state(false);
  let treeVisible = $state(false);
  let infoVisible = $state(false);

  onMount(() => {
    if (!sectionRef) return;

    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            isVisible = true;
            setTimeout(() => { treeVisible = true; }, 300);
            setTimeout(() => { infoVisible = true; }, 800);
          }
        });
      },
      { threshold: 0.15 }
    );

    observer.observe(sectionRef);
    return () => observer.disconnect();
  });
</script>

<section
  bind:this={sectionRef}
  id="store-architecture"
  class="architecture-section snap-section bg-bg-secondary"
  aria-labelledby="store-architecture-title"
>
  <!-- Background -->
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-orb gradient-orb-1"></div>
    <div class="gradient-orb gradient-orb-2"></div>
    <div class="dot-pattern"></div>
  </div>

  <div class="content-wrapper max-w-6xl mx-auto relative z-10">
    <h2
      id="store-architecture-title"
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

    <!-- Architecture Diagram -->
    <div class="diagram-container" class:visible={treeVisible}>
      <div class="tree">
        <!-- Root: Store -->
        <div class="tree-node root-node">
          <div class="node-box root">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" class="node-icon" aria-hidden="true">
              <path stroke-linecap="round" stroke-linejoin="round" d="M20.25 6.375c0 2.278-3.694 4.125-8.25 4.125S3.75 8.653 3.75 6.375m16.5 0c0-2.278-3.694-4.125-8.25-4.125S3.75 4.097 3.75 6.375m16.5 0v11.25c0 2.278-3.694 4.125-8.25 4.125s-8.25-1.847-8.25-4.125V6.375" />
            </svg>
            <span>{translations.store}</span>
          </div>
        </div>

        <!-- Connector line -->
        <div class="connector-vertical" aria-hidden="true">
          <div class="connector-line"></div>
        </div>

        <!-- Supervisor -->
        <div class="tree-node supervisor-node">
          <div class="node-box supervisor">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" class="node-icon" aria-hidden="true">
              <path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75L11.25 15 15 9.75m-3-7.036A11.959 11.959 0 013.598 6 11.99 11.99 0 003 9.749c0 5.592 3.824 10.29 9 11.623 5.176-1.332 9-6.03 9-11.622 0-1.31-.21-2.571-.598-3.751h-.152c-3.196 0-6.1-1.248-8.25-3.285z" />
            </svg>
            <span>{translations.supervisor}</span>
          </div>
        </div>

        <!-- Connector branch -->
        <div class="connector-branch" aria-hidden="true">
          <div class="branch-line"></div>
        </div>

        <!-- Children -->
        <div class="tree-children">
          <div class="child-node">
            <div class="node-box child accent-secondary">
              <span class="node-label">{translations.bucketServer}</span>
              <span class="node-badge">&times; N</span>
            </div>
          </div>
          <div class="child-node">
            <div class="node-box child accent-primary">
              <span class="node-label">{translations.queryManager}</span>
            </div>
          </div>
          <div class="child-node">
            <div class="node-box child accent-secondary">
              <span class="node-label">{translations.eventBus}</span>
            </div>
          </div>
          <div class="child-node">
            <div class="node-box child accent-primary">
              <span class="node-label">{translations.ttlManager}</span>
            </div>
          </div>
          <div class="child-node">
            <div class="node-box child accent-secondary">
              <span class="node-label">{translations.persistence}</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Info Cards -->
    <div class="info-cards grid md:grid-cols-2 gap-6 mt-10 md:mt-14" class:visible={infoVisible}>
      <div class="info-card glass-card">
        <div class="info-icon accent-secondary" aria-hidden="true">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
            <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 6A2.25 2.25 0 016 3.75h2.25A2.25 2.25 0 0110.5 6v2.25a2.25 2.25 0 01-2.25 2.25H6a2.25 2.25 0 01-2.25-2.25V6zM3.75 15.75A2.25 2.25 0 016 13.5h2.25a2.25 2.25 0 012.25 2.25V18a2.25 2.25 0 01-2.25 2.25H6A2.25 2.25 0 013.75 18v-2.25zM13.5 6a2.25 2.25 0 012.25-2.25H18A2.25 2.25 0 0120.25 6v2.25A2.25 2.25 0 0118 10.5h-2.25a2.25 2.25 0 01-2.25-2.25V6zM13.5 15.75a2.25 2.25 0 012.25-2.25H18a2.25 2.25 0 012.25 2.25V18A2.25 2.25 0 0118 20.25h-2.25A2.25 2.25 0 0113.5 18v-2.25z" />
          </svg>
        </div>
        <p class="info-text">{translations.bucketIsolation}</p>
      </div>
      <div class="info-card glass-card">
        <div class="info-icon accent-primary" aria-hidden="true">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
            <path stroke-linecap="round" stroke-linejoin="round" d="M19.5 12c0-1.232-.046-2.453-.138-3.662a4.006 4.006 0 00-3.7-3.7 48.678 48.678 0 00-7.324 0 4.006 4.006 0 00-3.7 3.7c-.017.22-.032.441-.046.662M19.5 12l3-3m-3 3l-3-3m-12 3c0 1.232.046 2.453.138 3.662a4.006 4.006 0 003.7 3.7 48.656 48.656 0 007.324 0 4.006 4.006 0 003.7-3.7c.017-.22.032-.441.046-.662M4.5 12l3 3m-3-3l-3 3" />
          </svg>
        </div>
        <p class="info-text">{translations.reactiveLayer}</p>
      </div>
    </div>
  </div>
</section>

<style>
  .architecture-section {
    position: relative;
    overflow: hidden;
  }

  /* Background */
  .background-elements {
    position: absolute;
    inset: 0;
    pointer-events: none;
  }

  .gradient-orb {
    position: absolute;
    border-radius: 50%;
    filter: blur(100px);
    opacity: 0.08;
  }

  .gradient-orb-1 {
    width: 500px;
    height: 500px;
    background: var(--color-accent-secondary);
    top: -150px;
    left: -150px;
    animation: orbFloat1 20s ease-in-out infinite;
  }

  .gradient-orb-2 {
    width: 400px;
    height: 400px;
    background: var(--color-accent-primary);
    bottom: -100px;
    right: -100px;
    animation: orbFloat2 18s ease-in-out infinite;
  }

  .dot-pattern {
    position: absolute;
    inset: 0;
    background-image: radial-gradient(rgba(0, 212, 255, 0.06) 1px, transparent 1px);
    background-size: 24px 24px;
  }

  @keyframes orbFloat1 {
    0%, 100% { transform: translate(0, 0); }
    50% { transform: translate(50px, 30px); }
  }

  @keyframes orbFloat2 {
    0%, 100% { transform: translate(0, 0); }
    50% { transform: translate(-40px, -30px); }
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

  /* Diagram */
  .diagram-container {
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out, transform 0.6s ease-out;
  }

  .diagram-container.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .tree {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0;
  }

  /* Node boxes */
  .node-box {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.75rem 1.25rem;
    border-radius: 10px;
    font-weight: 600;
    font-size: 0.95rem;
    color: var(--color-text-primary);
    transition: box-shadow 0.3s ease, transform 0.3s ease;
  }

  .node-box:hover {
    transform: translateY(-2px);
  }

  .node-icon {
    width: 20px;
    height: 20px;
  }

  .node-box.root {
    background: rgba(0, 212, 255, 0.15);
    border: 1px solid rgba(0, 212, 255, 0.4);
    color: var(--color-accent-secondary);
    font-size: 1.1rem;
  }

  .node-box.root:hover {
    box-shadow: 0 0 25px rgba(0, 212, 255, 0.2);
  }

  .node-box.supervisor {
    background: rgba(0, 255, 136, 0.1);
    border: 1px solid rgba(0, 255, 136, 0.35);
    color: var(--color-accent-primary);
    font-size: 0.85rem;
  }

  .node-box.supervisor:hover {
    box-shadow: 0 0 25px rgba(0, 255, 136, 0.15);
  }

  .node-box.child {
    padding: 0.5rem 1rem;
    font-size: 0.8rem;
    font-weight: 500;
    background: rgba(26, 26, 46, 0.8);
    border: 1px solid var(--color-border);
  }

  .node-box.child.accent-secondary {
    border-color: rgba(0, 212, 255, 0.25);
  }

  .node-box.child.accent-primary {
    border-color: rgba(0, 255, 136, 0.25);
  }

  .node-box.child.accent-secondary:hover {
    border-color: rgba(0, 212, 255, 0.5);
    box-shadow: 0 0 20px rgba(0, 212, 255, 0.1);
  }

  .node-box.child.accent-primary:hover {
    border-color: rgba(0, 255, 136, 0.5);
    box-shadow: 0 0 20px rgba(0, 255, 136, 0.1);
  }

  .node-label {
    color: var(--color-text-primary);
  }

  .node-badge {
    font-size: 0.7rem;
    padding: 0.1rem 0.4rem;
    background: rgba(0, 212, 255, 0.15);
    border-radius: 4px;
    color: var(--color-accent-secondary);
    font-weight: 600;
  }

  /* Connectors */
  .connector-vertical {
    display: flex;
    justify-content: center;
    height: 32px;
  }

  .connector-line {
    width: 2px;
    height: 100%;
    background: linear-gradient(to bottom, rgba(0, 212, 255, 0.4), rgba(0, 255, 136, 0.4));
  }

  .connector-branch {
    display: flex;
    justify-content: center;
    height: 32px;
    position: relative;
    width: 100%;
    max-width: 800px;
  }

  .branch-line {
    width: 2px;
    height: 100%;
    background: rgba(0, 255, 136, 0.3);
    position: absolute;
    left: 50%;
  }

  /* Children row */
  .tree-children {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 0.75rem;
    max-width: 800px;
    position: relative;
  }

  .tree-children::before {
    content: '';
    position: absolute;
    top: 0;
    left: 10%;
    right: 10%;
    height: 2px;
    background: linear-gradient(90deg,
      rgba(0, 212, 255, 0.3),
      rgba(0, 255, 136, 0.3),
      rgba(0, 212, 255, 0.3),
      rgba(0, 255, 136, 0.3),
      rgba(0, 212, 255, 0.3)
    );
  }

  .child-node {
    position: relative;
    padding-top: 16px;
  }

  .child-node::before {
    content: '';
    position: absolute;
    top: 0;
    left: 50%;
    width: 2px;
    height: 16px;
    background: rgba(0, 212, 255, 0.3);
  }

  /* Info cards */
  .info-cards {
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.6s ease-out, transform 0.6s ease-out;
  }

  .info-cards.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .glass-card {
    background: rgba(26, 26, 46, 0.6);
    border: 1px solid var(--color-border);
    border-radius: 12px;
    backdrop-filter: blur(12px);
    padding: 1.25rem;
    display: flex;
    gap: 1rem;
    align-items: flex-start;
    transition: border-color 0.3s ease;
  }

  .glass-card:hover {
    border-color: rgba(0, 212, 255, 0.3);
  }

  .info-icon {
    width: 36px;
    height: 36px;
    flex-shrink: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 8px;
  }

  .info-icon svg {
    width: 20px;
    height: 20px;
  }

  .info-icon.accent-secondary {
    background: rgba(0, 212, 255, 0.1);
    color: var(--color-accent-secondary);
  }

  .info-icon.accent-primary {
    background: rgba(0, 255, 136, 0.1);
    color: var(--color-accent-primary);
  }

  .info-text {
    color: var(--color-text-secondary);
    font-size: 0.9rem;
    line-height: 1.6;
  }

  /* Responsive */
  @media (max-width: 768px) {
    .tree-children {
      gap: 0.5rem;
    }

    .node-box.child {
      font-size: 0.72rem;
      padding: 0.4rem 0.75rem;
    }

    .node-box.root {
      font-size: 0.95rem;
    }

    .node-box.supervisor {
      font-size: 0.78rem;
    }

    .gradient-orb-1,
    .gradient-orb-2 {
      opacity: 0.05;
    }
  }

  /* Reduced motion */
  @media (prefers-reduced-motion: reduce) {
    .section-title,
    .section-subtitle,
    .diagram-container,
    .info-cards {
      transition: none;
      opacity: 1;
      transform: none;
    }

    .gradient-orb-1,
    .gradient-orb-2 {
      animation: none;
    }

    .node-box:hover {
      transform: none;
    }
  }
</style>
