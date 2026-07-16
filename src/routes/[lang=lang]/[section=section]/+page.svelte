<script lang="ts">
  import { getDictionary } from "$lib/dictionaries";
  import PostList from "$lib/components/PostList.svelte";
  import PrintedDivider from "$lib/components/PrintedDivider.svelte";
  import PrintedLabel from "$lib/components/PrintedLabel.svelte";
  import PrintedPageTitle from "$lib/components/PrintedPageTitle.svelte";
  import PrintedSection from "$lib/components/PrintedSection.svelte";
  import Seo from "$lib/components/Seo.svelte";

  let { data } = $props();

  let lang = $derived(data.lang);
  let section = $derived(data.section);
  let dictionary = $derived(getDictionary(lang));
  let sectionLabel = $derived(dictionary.labels[section]);

  function ratingDots(rating: number) {
    const full = Math.floor(rating);
    const half = rating % 1 >= 0.5 ? 1 : 0;
    return { full, half, empty: 5 - full - half };
  }
</script>

<Seo
  {lang}
  title="{sectionLabel} - {dictionary.meta.websiteName}"
  description={sectionLabel}
  path={dictionary.urls[section]}
/>

<div>
  <!-- Header -->
  <PrintedSection>
    <PrintedPageTitle icon={section === "life" ? "draft" : "window"}>
      {sectionLabel}
    </PrintedPageTitle>
    <p class="font-serif text-xs text-printer-ink-light dark:text-printer-ink-dark/50">
      {dictionary.labels.entries(data.posts.length)}
    </p>
  </PrintedSection>

  {#if section === "life"}
    <!-- Archive sections - links to subpages -->
    <PrintedSection label={dictionary.labels.activity} labelIcon="pulse">
      <div class="flex flex-wrap gap-2">
        <a href="{dictionary.urls.life}/reading">
          <PrintedLabel variant="default" icon="book">
            {dictionary.labels.reading}
          </PrintedLabel>
        </a>
        <a href="{dictionary.urls.life}/films">
          <PrintedLabel variant="default" icon="film">
            {dictionary.labels.films}
          </PrintedLabel>
        </a>
        <a href="{dictionary.urls.life}/music">
          <PrintedLabel variant="default" icon="music">
            {dictionary.labels.music}
          </PrintedLabel>
        </a>
      </div>
    </PrintedSection>

    <PrintedDivider style="dashed" />
  {/if}

  <!-- Categories as label strips -->
  <PrintedSection label={dictionary.labels.categories} labelIcon="tag">
    <div class="flex flex-wrap gap-1.5 mb-2">
      {#each data.categories as category (category.slug)}
        <a href={category.permalink[lang]}>
          <PrintedLabel variant="default">
            {category.name[lang]}
            <span class="opacity-50">({category.count[lang]})</span>
          </PrintedLabel>
        </a>
      {/each}
    </div>
  </PrintedSection>

  <PrintedDivider style="dashed" />

  <!-- Post list -->
  <PostList posts={data.posts} {lang} />

  {#if section === "posts"}
    <PrintedDivider style="dashed" />

    <!-- Recommended Tools -->
    <PrintedSection label={dictionary.labels.recommended} labelIcon="tools">
      <div class="flex flex-col gap-1">
        {#each dictionary.tools as tool, index (tool.name)}
          {@const dots = ratingDots(tool.rating)}
          <div>
            <a
              href={tool.link}
              target="_blank"
              rel="noopener"
              class="group flex items-center gap-3 py-3 -mx-2 px-2 rounded-md hover:bg-printer-ink/3 dark:hover:bg-printer-ink-dark/3 transition-colors"
            >
              <img
                src={tool.icon}
                alt={dictionary.labels.icon(tool.name)}
                class="h-10 w-10 rounded-lg border border-printer-ink/10 dark:border-printer-ink-dark/10 shrink-0"
                width="40"
                height="40"
                loading="lazy"
              />
              <div class="min-w-0 flex-1">
                <div class="flex items-center gap-2">
                  <span
                    class="font-mono text-sm font-medium text-printer-ink dark:text-printer-ink-dark group-hover:text-printer-accent dark:group-hover:text-printer-accent-dark transition-colors"
                  >
                    {tool.name}
                  </span>
                  <div class="flex items-center gap-0.5">
                    {#each Array.from({ length: dots.full }), i (i)}
                      <div
                        class="w-1.5 h-1.5 rounded-full bg-printer-accent dark:bg-printer-accent-dark"
                      ></div>
                    {/each}
                    {#if dots.half}
                      <div
                        class="w-1.5 h-1.5 rounded-full bg-printer-accent/40 dark:bg-printer-accent-dark/40"
                      ></div>
                    {/if}
                    {#each Array.from({ length: dots.empty }), i (i)}
                      <div
                        class="w-1.5 h-1.5 rounded-full bg-printer-ink/10 dark:bg-printer-ink-dark/10"
                      ></div>
                    {/each}
                    <span
                      class="font-mono text-[9px] text-printer-ink-light dark:text-printer-ink-dark/40 ml-1 tabular-nums"
                    >
                      {tool.rating.toFixed(1)}
                    </span>
                  </div>
                </div>
                <p
                  class="font-mono text-[10px] text-printer-ink-light dark:text-printer-ink-dark/40 mt-0.5 line-clamp-1"
                >
                  {tool.summary}
                </p>
                <div class="flex items-center gap-2 mt-1">
                  {#if tool.platform}
                    <PrintedLabel variant="muted">{tool.platform}</PrintedLabel>
                  {/if}
                  {#if tool.pricing}
                    <PrintedLabel variant="default">{tool.pricing}</PrintedLabel>
                  {/if}
                </div>
              </div>
              <span
                class="font-mono text-[10px] text-printer-ink-light dark:text-printer-ink-dark/30 group-hover:text-printer-accent dark:group-hover:text-printer-accent-dark transition-colors shrink-0"
              >
                →
              </span>
            </a>
            {#if index < dictionary.tools.length - 1}
              <div
                class="border-b border-dotted border-printer-ink/5 dark:border-printer-ink-dark/5"
              ></div>
            {/if}
          </div>
        {/each}
      </div>
    </PrintedSection>
  {:else}
    <PrintedDivider style="dashed" />
  {/if}
</div>
