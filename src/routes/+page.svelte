<script lang="ts">
  import { onMount } from 'svelte';

  import { profile } from '../lib/data';
  import ProjectCard, {
    type Repository,
  } from '../lib/components/ProjectCard.svelte';
  import langColorsData from './langColors.json';

  const langColors: Record<string, string> = langColorsData;

  interface GitHubRepo {
    name: string;
    description: string | null;
    language: string | null;
    languages_url: string;
    html_url: string;
    stargazers_count: number;
    topics?: string[];
  }

  let repos = $state<Repository[]>([]);
  let loading = $state(true);

  onMount(async () => {
    const CACHE_KEY = 'github_repos_cache';
    const CACHE_TIME = 1000 * 60 * 30; // 30 minutes

    const cachedData = localStorage.getItem(CACHE_KEY);
    if (cachedData) {
      const { timestamp, data } = JSON.parse(cachedData);
      if (Date.now() - timestamp < CACHE_TIME) {
        repos = data;
        loading = false;
        return;
      }
    }

    try {
      const response = await fetch(
        'https://api.github.com/users/MKMonkeyCat/repos?sort=updated&per_page=6'
      );
      const data = await response.json();

      const repoDetails = await Promise.all(
        data.map(async (repo: GitHubRepo) => {
          // Fetch languages for each repo
          const langRes = await fetch(repo.languages_url);
          const langData: Record<string, number> = await langRes.json();

          const totalSize = Object.values(langData).reduce((a, b) => a + b, 0);
          const languages = Object.entries(langData).map(([name, size]) => ({
            name,
            percentage: totalSize > 0 ? (size / totalSize) * 100 : 0,
            color: langColors[name] || '#888',
          }));

          return {
            name: repo.name,
            description: repo.description || 'No description provided.',
            lang: repo.language || 'Unknown',
            color: (repo.language && langColors[repo.language]) || '#888',
            tags: repo.topics || [],
            link: repo.html_url,
            stars: repo.stargazers_count,
            languages,
          };
        })
      );

      repos = repoDetails;
      localStorage.setItem(
        CACHE_KEY,
        JSON.stringify({ timestamp: Date.now(), data: repoDetails })
      );
    } catch (error) {
      console.error('Failed to fetch repos:', error);
    } finally {
      loading = false;
    }
  });

  const year = new Date().getFullYear();
</script>

<main>
  <div class="bg-glow"></div>
  <header class="anime-card header-section">
    <div class="header-content">
      <div class="avatar-container">
        <div class="avatar-glow"></div>
        <img src={profile.avatar} alt={profile.name} class="avatar" />
      </div>
      <div class="info">
        <h1 class="glitch-text" data-text={profile.name}>{profile.name}</h1>
        <div class="title-badge">{profile.title}</div>
        <div class="location-tag">NODE: {profile.location}</div>
        <div class="links">
          <a
            href={profile.github}
            target="_blank"
            rel="noreferrer"
            class="link-btn">GitHub</a
          >
          <a
            href={profile.repos}
            target="_blank"
            rel="noreferrer"
            class="link-btn">Repositories</a
          >
          <a
            href={profile.website}
            target="_blank"
            rel="noreferrer"
            class="link-btn">Website</a
          >
          {#if profile.socials}
            {#each profile.socials as social}
              <a
                href={social.link}
                target="_blank"
                rel="noreferrer"
                class="link-btn">{social.platform}</a
              >
            {/each}
          {/if}
        </div>
      </div>
    </div>
  </header>

  <section class="anime-card bio-section">
    <h2>DATA SOURCE</h2>
    <p class="bio-text">{profile.bio}</p>
  </section>

  <div class="grid">
    <section class="anime-card skill-section">
      <h2>CAPABILITIES</h2>
      {#each profile.skills as skillGroup}
        <div class="skill-category">
          <h3>{skillGroup.category}</h3>
          <div class="tags">
            {#each skillGroup.items as item}
              <span class="tag">{item}</span>
            {/each}
          </div>
        </div>
      {/each}
    </section>

    <section class="anime-card exp-section">
      <h2>LOGS</h2>
      <div class="timeline">
        {#each profile.experiences as exp}
          <div class="exp-item">
            <span class="period">{exp.period}</span>
            <div class="exp-content">
              <strong class="company">{exp.company}</strong>
              <div class="role">{exp.role}</div>
              <p>{exp.description}</p>
            </div>
          </div>
        {/each}
      </div>
    </section>
  </div>

  <section class="anime-card projects-section">
    <h2>DEPLOYMENTS</h2>
    {#if loading}
      <div class="loading-state">FETCHING_DATA...</div>
    {:else}
      <div class="projects-grid">
        {#each repos as project}
          <ProjectCard {project} />
        {/each}
      </div>
    {/if}
  </section>

  <footer class="footer">
    <div class="footer-line"></div>
    <p class="sub-footer">© {year} {profile.name}</p>
  </footer>
</main>

<style>
  .bg-glow {
    position: fixed;
    top: 50%;
    left: 50%;
    width: 100vw;
    height: 100vh;
    background: radial-gradient(
      circle at center,
      rgba(0, 243, 255, 0.03) 0%,
      transparent 70%
    );
    transform: translate(-50%, -50%);
    pointer-events: none;
    z-index: -1;
  }

  .header-content {
    display: flex;
    align-items: center;
    gap: 4rem;
    flex-wrap: wrap;
  }

  .info {
    flex: 1;
    min-width: 0;
  }

  .avatar-container {
    position: relative;
    padding: 10px;
  }

  .avatar-glow {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: linear-gradient(
      45deg,
      var(--accent-color),
      var(--secondary-color)
    );
    filter: blur(20px);
    opacity: 0.3;
    border-radius: 50%;
  }

  .avatar {
    width: 180px;
    height: 180px;
    border-radius: 50%;
    position: relative;
    z-index: 1;
    border: 3px solid rgba(255, 255, 255, 0.1);
    box-shadow: 0 0 40px rgba(0, 243, 255, 0.1);
  }

  .title-badge {
    background: rgba(0, 243, 255, 0.1);
    color: var(--accent-color);
    padding: 4px 12px;
    border-radius: 6px;
    font-weight: 800;
    font-size: 0.9rem;
    display: inline-block;
    margin-bottom: 1rem;
    border: 1px solid rgba(0, 243, 255, 0.2);
  }

  .location-tag {
    font-size: 0.85rem;
    color: #888;
    margin-bottom: 1.5rem;
  }

  .links {
    display: flex;
    gap: 1rem;
    flex-wrap: wrap;
  }

  .link-btn {
    padding: 6px 16px;
    background: rgba(255, 255, 255, 0.05);
    border: 1px solid var(--border-color);
    border-radius: 8px;
    font-size: 0.85rem;
    color: #fff;
    font-weight: 600;
  }

  .link-btn:hover {
    background: var(--accent-color);
    color: #000;
    border-color: var(--accent-color);
  }

  .bio-text {
    font-size: 1.1rem;
    color: #bbb;
    line-height: 1.8;
  }

  .grid {
    display: grid;
    grid-template-columns: 1fr 1.2fr;
    gap: 2rem;
  }

  @media (max-width: 900px) {
    .grid {
      grid-template-columns: 1fr;
    }
    .header-content {
      flex-direction: column;
      text-align: center;
      gap: 2rem;
    }
    .info {
      width: 100%;
    }
    .links {
      justify-content: center;
    }
  }

  @media (max-width: 600px) {
    .glitch-text {
      font-size: 2.2rem;
      word-wrap: break-word;
      overflow-wrap: break-word;
    }
    .avatar {
      width: 140px;
      height: 140px;
    }
    .projects-grid {
      grid-template-columns: 1fr;
    }
    .timeline {
      padding-left: 1.5rem;
    }
    .exp-item::before {
      left: -1.85rem;
    }
    .bio-text {
      font-size: 1rem;
    }
  }

  .skill-category h3 {
    font-size: 0.8rem;
    color: #666;
    letter-spacing: 2px;
    margin-bottom: 1rem;
  }

  .timeline {
    border-left: 1px solid var(--border-color);
    padding-left: 2rem;
  }

  .exp-item {
    margin-bottom: 2.5rem;
    position: relative;
  }

  .exp-item::before {
    content: '';
    position: absolute;
    left: -2.35rem;
    top: 10px;
    width: 10px;
    height: 10px;
    background: var(--accent-color);
    box-shadow: 0 0 10px var(--accent-color);
    border-radius: 2px;
  }

  .company {
    color: #fff;
    font-size: 1.1rem;
  }

  .role {
    color: var(--accent-color);
    font-weight: 700;
  }

  .period {
    font-size: 0.75rem;
    color: #555;
    font-weight: 800;
  }

  .projects-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
    gap: 2rem;
  }

  .loading-state {
    text-align: center;
    padding: 3rem;
    font-weight: 900;
    color: var(--accent-color);
    letter-spacing: 4px;
    animation: pulse 1.5s infinite;
  }

  @keyframes pulse {
    0%,
    100% {
      opacity: 1;
    }
    50% {
      opacity: 0.3;
    }
  }

  .glitch-text {
    font-size: 3.5rem;
    margin: 0;
  }

  .footer {
    text-align: center;
    margin-top: 3rem;
    color: #444;
  }

  .footer-line {
    height: 1px;
    background: linear-gradient(
      90deg,
      transparent,
      var(--border-color),
      transparent
    );
    margin-bottom: 2rem;
  }

  .sub-footer {
    font-size: 0.7rem;
    margin-top: 0.5rem;
  }
</style>
