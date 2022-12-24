<script lang="ts">
  import "../app.css";
  import { page } from "$app/stores";
  import { onMount } from "svelte";
  import { fly } from "svelte/transition";
  import { SvelteToast } from '@zerodevx/svelte-toast'

  const links = [
    {
      to: "/",
      name: "Home",
      ref: null as unknown as HTMLAnchorElement,
    },
    {
      to: "/feed",
      name: "Feed",
      ref: null as unknown as HTMLAnchorElement,
    },
    {
      to: "/create",
      name: "Create",
      ref: null as unknown as HTMLAnchorElement,
    },
    {
      to: "/leaderboard",
      name: "Leaderboard",
      ref: null as unknown as HTMLAnchorElement,
    },
    {
      to: "/account",
      name: "Account",
      ref: null as unknown as HTMLAnchorElement,
    },
  ];

  let route = null as unknown as string;

  let navContainerRef = null as unknown as HTMLDivElement;
  let navBottomWidth = null as unknown as number;
  let navBottomLeft = null as unknown as number;

  const calculatePositioning = (link: string) => {
    if (!navContainerRef) return;
    const containerBounds = navContainerRef.getBoundingClientRect();
    const linkBounds = links.find(({ to }) => to === link)?.ref.getBoundingClientRect() as DOMRect;
    if (!linkBounds) {
      navBottomLeft = null as unknown as number;
      navBottomWidth = null as unknown as number;
      return;
    }
    navBottomLeft = linkBounds.left - containerBounds.left;
    navBottomWidth = linkBounds.width;
  };

  page.subscribe(({ route: link }) => {
    if (!link || !link.id) return;
    route = link.id as string;
    calculatePositioning(route);
  });
  onMount(() => $page.route && calculatePositioning($page.route.id as string));
</script>

<div
  class="fixed top-0 left-0 right-0 h-12 z-10 border-b-2 border-black bg-white flex items-center"
>
  <a href="/" class="h-10 w-10 flex items-end">
    <img src="/favicon.ico" alt="icon"/>
    izzler.cf
  </a>
  <div class="flex relative gap-5 ml-auto mr-4" bind:this={navContainerRef}>
    {#each links as link}
      <a href={link.to} class="" bind:this={link.ref}>{link.name}</a>
    {/each}
    <div
      class="h-1 absolute bottom-0 bg-emerald-400 rounded-t-full transition-all"
      style={navBottomLeft !== null && navBottomWidth !== null
        ? `width: ${navBottomWidth}px; left: ${navBottomLeft}px; opacity: 1;`
        : "width: 0; left: 0; opacity: 0"}
    />
  </div>
</div>
<div class="h-12 mb-10" />
<div in:fly={{ y: -50, duration: 250, delay: 300 }} out:fly={{ y: -50, duration: 250 }}>
  <slot />
</div>

<SvelteToast />
