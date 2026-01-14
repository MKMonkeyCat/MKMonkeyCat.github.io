<script lang="ts">
  interface RepoLanguage {
    name: string;
    percentage: number;
    color: string;
  }

  export interface Repository {
    name: string;
    description: string;
    lang: string;
    color: string;
    tags: string[];
    link: string;
    stars: number;
    languages: RepoLanguage[];
  }

  let { project }: { project: Repository } = $props();
</script>

<div class="project-item" style="--repo-color: {project.color}">
  <div class="project-header">
    <span class="lang-dot"></span>
    <span class="lang-name">{project.lang}</span>
    {#if project.stars > 0}
      <span class="stars-count">★ {project.stars}</span>
    {/if}
    <h3>{project.name}</h3>
  </div>
  <p>{project.description}</p>

  <div class="lang-bar">
    {#each project.languages as l}
      <div
        class="lang-progress"
        style="width: {l.percentage}%; background-color: {l.color};"
        data-lang="{l.name} {l.percentage.toFixed(1)}%"
      ></div>
    {/each}
  </div>

  <div class="tags">
    {#each project.tags as tag}
      <span class="tag">{tag}</span>
    {/each}
  </div>
  <a href={project.link} target="_blank" rel="noreferrer" class="project-link">
    ACCESS_PROJECT >>
  </a>
</div>

<style>
  .project-item {
    background: rgba(255, 255, 255, 0.02);
    border: 1px solid var(--border-color);
    border-top: 4px solid var(--repo-color);
    border-radius: 12px;
    padding: 2rem;
    transition: all 0.3s;
    position: relative;
    display: flex;
    flex-direction: column;
  }

  @media (max-width: 600px) {
    .project-item {
      padding: 1.5rem;
    }
  }

  .project-item:hover {
    border-color: var(--repo-color);
    box-shadow: 0 10px 30px -10px var(--repo-color);
    transform: translateY(-5px);
  }

  h3 {
    margin: 0;
    font-size: 1.2rem;
    color: #fff;
  }

  p {
    color: #aaa;
    font-size: 0.9rem;
    line-height: 1.6;
    margin: 1rem 0;
    flex-grow: 1;
  }

  .stars-count {
    font-size: 0.75rem;
    color: #ff9f1c;
    font-weight: 800;
  }

  .project-header {
    display: flex;
    align-items: center;
    gap: 0.75rem;
    margin-bottom: 1rem;
    flex-wrap: wrap;
  }

  .lang-dot {
    width: 10px;
    height: 10px;
    background-color: var(--repo-color);
    border-radius: 50%;
    box-shadow: 0 0 10px var(--repo-color);
  }

  .lang-name {
    font-size: 0.75rem;
    font-weight: 800;
    color: var(--repo-color);
    text-transform: uppercase;
    letter-spacing: 1px;
  }

  .lang-bar {
    display: flex;
    height: 10px;
    width: 100%;
    background: rgba(255, 255, 255, 0.05);
    border-radius: 4px;
    margin: 1.5rem 0;
    border: 1px solid rgba(255, 255, 255, 0.1);
    position: relative;
  }

  .lang-progress {
    height: 100%;
    transition: all 0.2s ease;
    cursor: crosshair;
    position: relative;
  }

  .lang-progress:first-child {
    border-top-left-radius: 4px;
    border-bottom-left-radius: 4px;
  }
  .lang-progress:last-child {
    border-top-right-radius: 4px;
    border-bottom-right-radius: 4px;
  }

  .lang-progress:hover {
    z-index: 110;
    filter: brightness(1.3);
    transform: scaleY(1.5);
  }

  .lang-progress:hover::after {
    content: attr(data-lang);
    position: absolute;
    bottom: calc(100% + 10px);
    left: 50%;
    transform: translateX(-50%);
    background: #fff;
    color: #000;
    padding: 4px 10px;
    font-size: 0.7rem;
    font-weight: 900;
    white-space: nowrap;
    border-radius: 4px;
    box-shadow: 0 0 20px rgba(255, 255, 255, 0.3);
    pointer-events: none;
    z-index: 100;
    animation: slideUp 0.2s ease-out;
  }

  .lang-progress:hover::before {
    content: '';
    position: absolute;
    bottom: calc(100% + 5px);
    left: 50%;
    transform: translateX(-50%);
    border-left: 5px solid transparent;
    border-right: 5px solid transparent;
    border-top: 5px solid #fff;
    pointer-events: none;
    z-index: 100;
  }

  @keyframes slideUp {
    from {
      opacity: 0;
      transform: translate(-50%, 10px);
    }
    to {
      opacity: 1;
      transform: translate(-50%, 0);
    }
  }

  .tags {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
    margin-top: auto;
    max-height: 4.5rem;
    overflow: hidden;
  }

  .tag {
    font-size: 0.7rem;
    padding: 2px 8px;
    background: rgba(255, 255, 255, 0.05);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 4px;
    color: #888;
    white-space: nowrap;
  }

  .project-link {
    display: inline-block;
    margin-top: 1.5rem;
    font-weight: 900;
    color: var(--accent-color);
    font-size: 0.85rem;
    letter-spacing: 1px;
    text-decoration: none;
  }

  .project-link:hover {
    text-decoration: underline;
  }
</style>
