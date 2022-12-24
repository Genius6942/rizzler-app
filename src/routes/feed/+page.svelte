<script>
  import Post from "./post.svelte";
  import Fa from "svelte-fa/src/fa.svelte";
  import { faPencil } from "@fortawesome/free-solid-svg-icons";
</script>

<div class="text-center text-4xl font-bold text-emerald-900 mb-5">Top picks for you</div>
<div class="px-5">
  <Post
    image="https://nypost.com/wp-content/uploads/sites/2/2022/08/dog-eats-homework-reddit-1.jpg?quality=75&strip=all&w=768"
  />
</div>
<a href='/create'
  class="group fixed bottom-10 right-10 rounded-full w-16 h-16 bg-emerald-400 text-white flex justify-center items-center"
  ><span class="group-hover:scale-110 transition-all"><Fa icon={faPencil} scale={1.3}/></span>
</a>
