<script lang="ts">
  import { faArrowRight, faCloudArrowUp, faUpload } from "@fortawesome/free-solid-svg-icons";
  import Fa from "svelte-fa";
  import FuckFilter from "bad-words";
  import { toast } from "@zerodevx/svelte-toast";
  import Post from "../feed/post.svelte";

  const filter = new FuckFilter();

  let step = 0;
  let title = "";
  let content = "";
  const steps = 4;

  $: translate = step * 100;

  let file: null | File = null;

  const profanity_alert = async () => {
    toast.push(profanity_message);
  };

  const profanity_message =
    "looks like you used some of those big boy words. but we don't allow them... please remove the profanity from your post.";

  const verify = (step: number) => {
    if (
      (title.length > 0 && filter.clean(title) !== title) ||
      (content.length > 0 && filter.clean(content) !== content)
    ) {
      profanity_alert();
      return false;
    }
    if (step === 0 && title === "") {
      return false;
    }
    if (step === 2 && !file && content.length <= 0) {
      return false;
    }
  };

  let errors = Array(steps).fill(false);

  const showError = (step: number) => {
    errors[step] = true;
  };

  const next = () => {
    if (verify(step) === false) return showError(step);
    step = step + 1;
  };

  const fileUploadHandler = (files: FileList | null) => {
    if (!files || !files[0]) {
      file = null;
      return;
    }
    console.log(files[0].name);

    const regex = new RegExp(/[^\s]+(.*?).(jpg|jpeg|png|gif)$/);

    if (!regex.test(files[0].name)) {
      toast.push("Invalid file type. We only allow jpeg, jpg, png, and gif");
      return;
    }

    file = files[0];
  };

  $: imageBlobUrl = (file && URL.createObjectURL(file)) as string;

  const upload = () => {

  }
</script>

<!-- we need to precompile these classes so tailwind recongizes them -->
<div
  class="-translate-x-[0vw] -translate-x-[100vw] -translate-x-[200vw] -translate-x-[300vw] border-emerald-600 border-red-600"
/>

<div class="flex flex-col items-center justify-center">
  <div class="text-4xl text-emerald-400">Create a post</div>
  <div class="overflow-hidden w-screen h-[calc(100vh-8rem)] relative">
    <div class="flex w-[400vw] -translate-x-[{translate}vw] h-full transition-transform">
      <div class="flex flex-col justify-center items-center w-screen h-full gap-3">
        <div class="text-3xl text-emerald-400">1. Name your post</div>
        <input
          type="text"
          placeholder="title"
          bind:value={title}
          on:keydown={({ key }) => key === "Enter" && next()}
          class="w-96 rounded-xl border-dashed border-4 {errors[0]
            ? 'border-red-600'
            : 'border-emerald-600'} focus-within:outline-none focus-within:{errors[0]
            ? 'border-red-600'
            : 'border-emerald-600'} focus-within:border-solid {errors[0] && 'shake'}"
        />
        <button
          on:click={next}
          class="flex items-center gap-2 hover:gap-5 transition-all text-xl text-white p-3 rounded-xl bg-emerald-500"
          >Next <Fa icon={faArrowRight} />
        </button>
      </div>
      <div class="flex flex-col justify-center items-center w-screen h-full gap-3">
        <div class="text-3xl text-emerald-400">2. Some post content (optional)</div>
        <textarea
          type="text"
          placeholder="content"
          bind:value={content}
          on:keydown={({ key, ctrlKey }) => ctrlKey && key === "Enter" && next()}
          class="w-96 rounded-xl border-dashed border-4 resize-none p-3 overflow-hidden {errors[1]
            ? 'border-red-600'
            : 'border-emerald-600'} focus-within:outline-none focus-within:{errors[1]
            ? 'border-red-600'
            : 'border-emerald-600'} focus-within:border-solid {errors[1] && 'shake'}"
          on:input={({ currentTarget }) => {
            currentTarget.style.height = "";
            currentTarget.style.height = currentTarget.scrollHeight + "px";
          }}
        />
        <button
          on:click={next}
          class="flex items-center gap-2 hover:gap-5 transition-all text-xl text-white p-3 rounded-xl bg-emerald-500"
          >Next <Fa icon={faArrowRight} />
        </button>
      </div>
      <div class="flex flex-col justify-center items-center w-screen h-full gap-3">
        <div class="text-3xl text-emerald-400">
          3. upload image ({content.length > 0 ? "optional" : "required"})
        </div>
        <label
          for="file_upload"
          on:dragover={(e) => {
            e.preventDefault();
            e.currentTarget.style.borderStyle = "solid";
          }}
          on:dragleave={({ currentTarget, target }) => {
            if (currentTarget !== target) return;
            currentTarget.style.borderStyle = "dashed";
          }}
          on:drop={(e) => {
            e.preventDefault();
            if (
              !e.dataTransfer ||
              !e.dataTransfer.files ||
              !e.dataTransfer.files[0] ||
              !(e.dataTransfer.files[0] instanceof File)
            ) {
              toast.push(
                "Either there was no file, or your browser doesn't support drag and drop. Try clicking the upload button."
              );
              return;
            }

            file = e.dataTransfer.files[0];
          }}
          class="relative cursor-pointer w-96 rounded-xl border-dashed border-4 resize-none p-3 overflow-hidden {errors[2]
            ? 'border-red-600'
            : 'border-emerald-600'} focus-within:outline-none focus-within:{errors[2]
            ? 'border-red-600'
            : 'border-emerald-600'} focus-within:border-solid {errors[2] && 'shake'}"
        >
          <div class="flex gap-2 whitespace-nowrap text-ellipsis max-w-full">
            <Fa icon={faUpload} size="lg" />
            Upload or drop file ({file ? file.name : "none"})
          </div>
          {#if imageBlobUrl}
            <img
              src={imageBlobUrl}
              alt="uploaded file"
            />
          {/if}
        </label>

        <input
          id="file_upload"
          type="file"
          class="hidden"
          placeholder="content"
          on:input={({ currentTarget }) => fileUploadHandler(currentTarget.files)}
        />
        <button
          on:click={next}
          class="flex items-center gap-2 hover:gap-5 transition-all text-xl text-white p-3 rounded-xl bg-emerald-500"
          >Next <Fa icon={faArrowRight} />
        </button>
      </div>
      <div class="flex flex-col justify-center items-center w-screen h-full gap-3">
        <div class="text-3xl text-emerald-400">4. Confirm and send</div>
        <div class="overflow-auto p-3">
        <Post title={title} text={content} image={imageBlobUrl}/></div>
        <button
          on:click={upload}
          class="flex items-center gap-2 hover:gap-5 transition-all mb-10 text-xl text-white p-3 rounded-xl bg-emerald-500"
          >Go! <Fa icon={faCloudArrowUp} />
        </button>
      </div>
    </div>
  </div>
</div>

<style>
  .shake {
    animation: shake 0.25s linear;
  }

  @keyframes shake {
    0% {
      transform: translate(1px, 1px) rotate(0deg);
    }
    10% {
      transform: translate(-1px, -2px) rotate(-1deg);
    }
    20% {
      transform: translate(-3px, 0px) rotate(1deg);
    }
    30% {
      transform: translate(3px, 2px) rotate(0deg);
    }
    40% {
      transform: translate(1px, -1px) rotate(1deg);
    }
    50% {
      transform: translate(-1px, 2px) rotate(-1deg);
    }
    60% {
      transform: translate(-3px, 1px) rotate(0deg);
    }
    70% {
      transform: translate(3px, 1px) rotate(-1deg);
    }
    80% {
      transform: translate(-1px, -1px) rotate(1deg);
    }
    90% {
      transform: translate(1px, 2px) rotate(0deg);
    }
    100% {
      transform: translate(1px, -2px) rotate(-1deg);
    }
  }
</style>
