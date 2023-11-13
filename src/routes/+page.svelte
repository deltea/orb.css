<script lang="ts">
  import { onMount } from "svelte";
  import { fade } from "svelte/transition";

  interface OrbSettings {
    color1: string;
    color2: string;
    size: number;
    colorGlowRadius: number;
    whiteGlowRadius: number;
    borderRadius: number;
    isCircle: boolean;
  }

  let currentOrb: OrbSettings = {
    color1: "#f0f",
    color2: "#0ff",
    size: 300,
    whiteGlowRadius: 50,
    colorGlowRadius: 80,
    borderRadius: 50,
    isCircle: false,
  };

  let color1Picker: HTMLInputElement;
  let color2Picker: HTMLInputElement;
  let topOfPage = true;
  let mounted = false;
  let cssResult = "";
  let cssCopied = false;

  $: { currentOrb; saveSettings(); }
  $: cssResult = `width: ${currentOrb.size}px;\nheight: ${currentOrb.size}px;\nborder-radius: ${currentOrb.isCircle ? "50%" : `${currentOrb.borderRadius}px`};\nbox-shadow:\n\tinset 0 0 50px white,\n\tinset ${currentOrb.size / 10}px 0 80px ${currentOrb.color1},\n\tinset -${currentOrb.size / 10}px 0 80px ${currentOrb.color2},\n\t0 0 ${currentOrb.whiteGlowRadius}px white,\n\t-10px 0 ${currentOrb.colorGlowRadius}px ${currentOrb.color1},\n\t10px 0 ${currentOrb.colorGlowRadius}px ${currentOrb.color2};`;

  function checkTopOfPage() {
    topOfPage = window.scrollY === 0;
  }

  function saveSettings() {
    if (!mounted) return;
    localStorage.setItem("savedSettings", JSON.stringify(currentOrb));
  }

  function copyToClipboard() {
    navigator.clipboard.writeText(cssResult);
    cssCopied = true;
  }

  onMount(() => {
    mounted = true;

    checkTopOfPage();
    document.addEventListener("scroll", checkTopOfPage);

    const savedSettings = localStorage.getItem("savedSettings");
    if (savedSettings) {
      currentOrb = JSON.parse(savedSettings);
    }

    return () => document.removeEventListener("scroll", checkTopOfPage);
  });
</script>

<main class="min-h-screen bg-neutral-950 text-white flex flex-col justify-center items-center text-sm">
  {#if mounted}
    <div
      transition:fade
      class="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 duration-200"
      style={cssResult}></div>
  {/if}

  {#if topOfPage}
    <button
      class="fixed bottom-8 flex flex-col items-center"
      transition:fade={{ duration: 200 }}
      on:click={() => window.scrollBy(0, 500)}>
      <h2 class="text-base">Scroll to edit</h2>
      <iconify-icon icon="mdi:chevron-double-down" class="text-2xl"></iconify-icon>
    </button>
  {/if}

  <div class="h-screen mt-[100vh] flex items-center">
    <form
      class="bg-white bg-opacity-5 backdrop-blur-md border border-white border-opacity-20 p-6 rounded-xl w-[28rem] z-10 space-y-4"
      on:submit|preventDefault>
      <div class="space-y-1">
        <label for="size">Size</label>
        <input
          class="slider"
          type="range"
          name="size"
          id="size"
          min={120}
          max={300}
          bind:value={currentOrb.size} />
      </div>
      <div class="space-y-1">
        <label for="border-radius">Border Radius</label>
        <input
          class="slider"
          type="range"
          name="border-radius"
          id="border-radius"
          min={0}
          max={60}
          bind:value={currentOrb.borderRadius} />
      </div>
      <div class="relative space-y-1">
        <label for="color1">First Color</label>
        <button
          class="rounded-lg w-full block h-2 hover:h-8 duration-200"
          style="background-color: {currentOrb.color1};"
          on:click={() => color1Picker.click()}></button>
        <input
          class="w-full rounded-xl bg-black absolute top-0 left-0 invisible"
          type="color"
          name="color1"
          id="color1"
          bind:value={currentOrb.color1}
          bind:this={color1Picker}>
      </div>
      <div class="relative space-y-1">
        <label for="color2">Second Color</label>
        <button
          class="rounded-lg w-full block h-2 hover:h-8 duration-200"
          style="background-color: {currentOrb.color2};"
          on:click={() => color2Picker.click()}></button>
        <input
          class="w-full rounded-xl bg-black absolute top-0 left-0 invisible"
          type="color"
          name="color2"
          id="color2"
          bind:value={currentOrb.color2}
          bind:this={color2Picker}>
      </div>
      <div class="flex gap-16">
        <label for="circle">Circle</label>
        <input type="checkbox" name="circle" id="circle" bind:checked={currentOrb.isCircle}>
      </div>
      <div class="space-y-2">
        <label for="code">CSS</label>
        <pre id="code" class="rounded-lg font-mono">{cssResult}</pre>
      </div>
      <button
        class="bg-white bg-opacity-20 hover:bg-opacity-30 border border-white border-opacity-30 rounded-lg w-full h-12 duration-200 flex items-center justify-center gap-1"
        on:click={copyToClipboard}>
        {#if cssCopied}
          <iconify-icon
            icon="mdi:check"
            class="text-xl"
            transition:fade></iconify-icon>
        {/if}
        <span>Copy CSS code</span>
      </button>
    </form>
  </div>
</main>

<footer class="fixed bottom-0 flex justify-between w-full p-4 bg-transparent text-white text-sm">
  <span class="z-10">
    Inspired by <a href="https://css.glass" target="_blank"><strong>css.glass</strong></a>
  </span>
  <span>
    by <a href="https://github.com/thcheetah777" target="_blank"><strong>Leo</strong></a>
  </span>
</footer>
