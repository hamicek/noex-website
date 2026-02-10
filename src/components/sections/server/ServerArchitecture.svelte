<script lang="ts">
  import { onMount } from 'svelte';

  interface ServerArchitectureTranslations {
    title: string;
    subtitle: string;
    server: string;
    httpServer: string;
    supervisor: string;
    connectionServer: string;
    registry: string;
    rateLimiter: string;
    connectionIsolation: string;
    requestPipeline: string;
  }

  interface Props {
    translations: ServerArchitectureTranslations;
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
  id="server-architecture"
  class="architecture-section snap-section bg-bg-primary"
  aria-labelledby="server-architecture-title"
>
  <div class="background-elements" aria-hidden="true">
    <div class="gradient-orb gradient-orb-1"></div>
    <div class="gradient-orb gradient-orb-2"></div>
    <div class="dot-pattern"></div>
  </div>

  <div class="content-wrapper max-w-6xl mx-auto relative z-10">
    <h2
      id="server-architecture-title"
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
        <!-- Root: NoexServer -->
        <div class="tree-node root-node">
          <div class="node-box root">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" class="node-icon" aria-hidden="true">
              <path stroke-linecap="round" stroke-linejoin="round" d="M5.25 14.25h13.5m-13.5 0a3 3 0 01-3-3m3 3a3 3 0 100 6h13.5a3 3 0 100-6m-16.5-3a3 3 0 013-3h13.5a3 3 0 013 3m-19.5 0a4.5 4.5 0 01.9-2.7L5.737 5.1a3.375 3.375 0 012.7-1.35h7.126c1.062 0 2.062.5 2.7 1.35l2.587 3.45a4.5 4.5 0 01.9 2.7m0 0a3 3 0 01-3 3m0 3h.008v.008h-.008v-.008zm0-6h.008v.008h-.008v-.008zm-3 6h.008v.008h-.008v-.008zm0-6h.008v.008h-.008v-.008z" />
            </svg>
            <span>{translations.server}</span>
          </div>
        </div>

        <!-- Connector line -->
        <div class="connector-vertical" aria-hidden="true">
          <div class="connector-line"></div>
        </div>

        <!-- Branch connector -->
        <div class="connector-branch" aria-hidden="true">
          <div class="branch-line"></div>
        </div>

        <!-- Children row 1: HTTP + Supervisor -->
        <div class="tree-children">
          <div class="child-node">
            <div class="node-box child accent-violet">
              <span class="node-label">{translations.httpServer}</span>
            </div>
          </div>
          <div class="child-node">
            <div class="node-box child accent-green supervisor-box">
              <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" class="node-icon-small" aria-hidden="true">
                <path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75L11.25 15 15 9.75m-3-7.036A11.959 11.959 0 013.598 6 11.99 11.99 0 003 9.749c0 5.592 3.824 10.29 9 11.623 5.176-1.332 9-6.03 9-11.622 0-1.31-.21-2.571-.598-3.751h-.152c-3.196 0-6.1-1.248-8.25-3.285z" />
              </svg>
              <span class="node-label">{translations.supervisor}</span>
            </div>
          </div>
          <div class="child-node">
            <div class="node-box child accent-violet">
              <span class="node-label">{translations.registry}</span>
            </div>
          </div>
          <div class="child-node">
            <div class="node-box child accent-green">
              <span class="node-label">{translations.rateLimiter}</span>
              <span class="node-badge-optional">opt</span>
            </div>
          </div>
        </div>

        <!-- Sub-tree: ConnectionServer instances under supervisor -->
        <div class="sub-tree">
          <div class="sub-connector" aria-hidden="true">
            <div class="sub-connector-line"></div>
          </div>
          <div class="connection-instances">
            <div class="instance-node">
              <div class="node-box instance">
                <span class="node-label">{translations.connectionServer} #1</span>
              </div>
            </div>
            <div class="instance-node">
              <div class="node-box instance">
                <span class="node-label">{translations.connectionServer} #2</span>
              </div>
            </div>
            <div class="instance-node">
              <div class="node-box instance instance-dots">
                <span class="node-label">{translations.connectionServer} #N</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Info Cards -->
    <div class="info-cards grid md:grid-cols-2 gap-6 mt-10 md:mt-14" class:visible={infoVisible}>
      <div class="info-card glass-card">
        <div class="info-icon accent-violet" aria-hidden="true">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
            <path stroke-linecap="round" stroke-linejoin="round" d="M8.25 3v1.5M4.5 8.25H3m18 0h-1.5M4.5 12H3m18 0h-1.5m-15 3.75H3m18 0h-1.5M8.25 19.5V21M12 3v1.5m0 15V21m3.75-18v1.5m0 15V21m-9-1.5h10.5a2.25 2.25 0 002.25-2.25V6.75a2.25 2.25 0 00-2.25-2.25H6.75A2.25 2.25 0 004.5 6.75v10.5a2.25 2.25 0 002.25 2.25z" />
          </svg>
        </div>
        <p class="info-text">{translations.connectionIsolation}</p>
      </div>
      <div class="info-card glass-card">
        <div class="info-icon accent-green" aria-hidden="true">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
            <path stroke-linecap="round" stroke-linejoin="round" d="M19.5 12c0-1.232-.046-2.453-.138-3.662a4.006 4.006 0 00-3.7-3.7 48.678 48.678 0 00-7.324 0 4.006 4.006 0 00-3.7 3.7c-.017.22-.032.441-.046.662M19.5 12l3-3m-3 3l-3-3m-12 3c0 1.232.046 2.453.138 3.662a4.006 4.006 0 003.7 3.7 48.656 48.656 0 007.324 0 4.006 4.006 0 003.7-3.7c.017-.22.032-.441.046-.662M4.5 12l3 3m-3-3l-3 3" />
          </svg>
        </div>
        <p class="info-text">{translations.requestPipeline}</p>
      </div>
    </div>
  </div>
</section>

<style>
  .architecture-section {
    position: relative;
    overflow: hidden;
  }

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
    background: var(--color-accent-primary);
    top: -150px;
    left: -150px;
    animation: orbFloat1 20s ease-in-out infinite;
  }

  .gradient-orb-2 {
    width: 400px;
    height: 400px;
    background: #a78bfa;
    bottom: -100px;
    right: -100px;
    animation: orbFloat2 18s ease-in-out infinite;
  }

  .dot-pattern {
    position: absolute;
    inset: 0;
    background-image: radial-gradient(rgba(167, 139, 250, 0.06) 1px, transparent 1px);
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

  .node-icon-small {
    width: 16px;
    height: 16px;
  }

  .node-box.root {
    background: rgba(167, 139, 250, 0.15);
    border: 1px solid rgba(167, 139, 250, 0.4);
    color: #a78bfa;
    font-size: 1.1rem;
  }

  .node-box.root:hover {
    box-shadow: 0 0 25px rgba(167, 139, 250, 0.2);
  }

  .node-box.child {
    padding: 0.5rem 1rem;
    font-size: 0.8rem;
    font-weight: 500;
    background: rgba(26, 26, 46, 0.8);
    border: 1px solid var(--color-border);
  }

  .node-box.child.accent-violet {
    border-color: rgba(167, 139, 250, 0.25);
  }

  .node-box.child.accent-green {
    border-color: rgba(0, 255, 136, 0.25);
  }

  .node-box.child.accent-violet:hover {
    border-color: rgba(167, 139, 250, 0.5);
    box-shadow: 0 0 20px rgba(167, 139, 250, 0.1);
  }

  .node-box.child.accent-green:hover {
    border-color: rgba(0, 255, 136, 0.5);
    box-shadow: 0 0 20px rgba(0, 255, 136, 0.1);
  }

  .supervisor-box {
    border-color: rgba(0, 255, 136, 0.35) !important;
    background: rgba(0, 255, 136, 0.05);
    color: var(--color-accent-primary);
  }

  .node-label {
    color: var(--color-text-primary);
  }

  .node-badge-optional {
    font-size: 0.6rem;
    padding: 0.05rem 0.35rem;
    background: rgba(167, 139, 250, 0.12);
    border-radius: 3px;
    color: #a78bfa;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 0.03em;
  }

  .node-box.instance {
    padding: 0.375rem 0.75rem;
    font-size: 0.72rem;
    font-weight: 500;
    background: rgba(167, 139, 250, 0.06);
    border: 1px solid rgba(167, 139, 250, 0.2);
    color: #c4b5fd;
  }

  .node-box.instance:hover {
    border-color: rgba(167, 139, 250, 0.4);
    box-shadow: 0 0 15px rgba(167, 139, 250, 0.1);
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
    background: linear-gradient(to bottom, rgba(167, 139, 250, 0.4), rgba(0, 255, 136, 0.4));
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
    background: rgba(167, 139, 250, 0.3);
    position: absolute;
    left: 50%;
  }

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
      rgba(167, 139, 250, 0.3),
      rgba(0, 255, 136, 0.3),
      rgba(167, 139, 250, 0.3),
      rgba(0, 255, 136, 0.3)
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
    background: rgba(167, 139, 250, 0.3);
  }

  /* Sub-tree for ConnectionServer instances */
  .sub-tree {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-top: 0;
  }

  .sub-connector {
    height: 24px;
    display: flex;
    justify-content: center;
  }

  .sub-connector-line {
    width: 2px;
    height: 100%;
    background: rgba(167, 139, 250, 0.2);
  }

  .connection-instances {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 0.5rem;
    position: relative;
  }

  .connection-instances::before {
    content: '';
    position: absolute;
    top: 0;
    left: 15%;
    right: 15%;
    height: 2px;
    background: rgba(167, 139, 250, 0.15);
  }

  .instance-node {
    position: relative;
    padding-top: 12px;
  }

  .instance-node::before {
    content: '';
    position: absolute;
    top: 0;
    left: 50%;
    width: 2px;
    height: 12px;
    background: rgba(167, 139, 250, 0.15);
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
    border-color: rgba(167, 139, 250, 0.3);
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

  .info-icon.accent-violet {
    background: rgba(167, 139, 250, 0.1);
    color: #a78bfa;
  }

  .info-icon.accent-green {
    background: rgba(0, 255, 136, 0.1);
    color: var(--color-accent-primary);
  }

  .info-text {
    color: var(--color-text-secondary);
    font-size: 0.9rem;
    line-height: 1.6;
  }

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

    .node-box.instance {
      font-size: 0.65rem;
      padding: 0.3rem 0.5rem;
    }

    .gradient-orb-1,
    .gradient-orb-2 {
      opacity: 0.05;
    }
  }

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
