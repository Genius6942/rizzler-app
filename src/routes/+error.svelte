<script>
  import { page } from "$app/stores";
  import Fa from "svelte-fa/src/fa.svelte";
  import { faChevronRight } from "@fortawesome/free-solid-svg-icons";
  let showDetails = false;

  const toggleDetails = () => {
    showDetails = !showDetails;
  };
</script>

<div class="flex items-center flex-col">
  <div class="flex items-center">
    <button
      class="mr-3 transition-transform {showDetails && 'rotate-90'} cursor-pointer hover:scale-110"
      on:click={toggleDetails}
    >
      <Fa icon={faChevronRight} on:click />
    </button>
    Error <span class="font-bold ml-1">{$page.status}</span>
  </div>
  {#if showDetails}
    <div class="overflow-hidden transition-all">{$page.error?.message}</div>
  {/if}
</div>
