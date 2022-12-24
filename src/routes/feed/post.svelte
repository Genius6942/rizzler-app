<script lang="ts">
  export let icon = "/rick.gif";
  export let upvoted = false;
  export let title = "so i was watching the dog and then...";
  export let text = "it ate my homework";
  export let image: string = "";
</script>

<div class="rounded-lg p-3 border-gray-500 border-2">
  <div class="flex items-center">
    <div
      class="rounded-full object-cover min-w-[50px] h-[50px] mr-3"
      style="background-image: url({icon}); background-size: 100%;"
    />
    <div class="font-bold text-2xl flex-grow font-roboto">
      {title}
    </div>
    <div class="min-w-[50px] font-bold text-xl ml-3">
      <div
        class="w-10 h-5 {!upvoted
          ? 'bg-gray-600'
          : 'bg-emerald-300'} cursor-pointer hover:-translate-y-1 transition-all hover:pb-1"
        style="clip-path: polygon(0 100%, 50% 0, 100% 100%);"
      />
      10k
    </div>
  </div>
  {text}
  {#if image && image !== ""}
    <img src={image} alt="" />
  {/if}
</div>
