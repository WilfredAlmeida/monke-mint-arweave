<script lang="ts">
  import Counter from "./lib/Counter.svelte";
</script>

<svelte:head>
    <title>MonkeMint</title>
  </svelte:head>

<main class="bg-custom-image">
  
  <div class="card">
    <Counter />
  </div>
</main>

<style>
  .bg-custom-image {
    background-image: url("./assets/bg.jpg");
    background-size: cover;
    background-position: center;
    background-repeat: repeat;
    height: 100vh;
  }

  .bg-custom-image::before {
    content: "";
    position: absolute;
    inset: 0;
    background-image: inherit;
    background-size: cover;
    background-position: inherit;
    background-repeat: inherit;
    filter: blur(5px);
    z-index: -3;
  }
</style>
