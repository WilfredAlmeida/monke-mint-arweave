<script>
  import "../app.css";
  import { onMount } from "svelte";
  import axios from "axios";

  let nftName = "";
  let nftDescription = "";
  let solanaAddresses = "";
  /**
   * @type {HTMLInputElement | null}
   */
  let imageInput = null;
  let loading = false;

  const submitForm = async () => {
    loading = true;

    try {
      if (!imageInput || !imageInput.files || !imageInput.files[0]) {
        alert("Image is required");
      }

      const imageBase64 = await toBase64(imageInput.files[0]);

      if (!imageBase64) {
        alert("Image is required");
      }

      if (
        nftName.length === 0 ||
        nftDescription.length === 0 ||
        solanaAddresses.length === 0
      ) {
        alert("All fields are required");
      }

      console.log({
        nftName,
        nftDescription,
        solanaAddresses: solanaAddresses
          .split(",")
          .map((address) => address.trim()),
        imageBase64,
      });

      const batch = [];

      solanaAddresses
        .split(",")
        .map((address) => batch.push({ receiverAddress: address.trim() }));

      const response = await axios.post(
        "https://devnet.underdogprotocol.com/v2/projects/52/nfts/batch",
        {
          name: nftName,
          description: nftDescription,
          image: imageBase64,
          batch: batch,
        },
        {
          headers: {
            Authorization:
              "Bearer ad16a160d62d8b.6a3e344e81e648a3b49bf52e33b1e63e",
          },
        }
      );

      console.log(response.data); // Handle the API response as needed

      if (response.status === 202) {
        alert("NFTs minted successfully");
      } else {
        alert("Something went wrong");
      }
    } catch (error) {
      console.error("Error:", error);
    } finally {
      loading = false;
    }
  };

  const toBase64 = (file) =>
    new Promise((resolve, reject) => {
      const reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = () => resolve(reader.result.split(",")[1]);
      reader.onerror = (error) => reject(error);
    });

  // Slider data
  let sliderImages = [
    "https://bafybeiese3bgyfewt2r3dxvgups2blc3rwh2utvidirxgxq527mhcv3ydy.ipfs.nftstorage.link",
    "https://c6fltba5toiaq2tg6lpskwu4txgfgglfcin4w2djf36lhbady73a.arweave.net/F4q5hB2bkAhqZvLfJVqcncxTGWUSG8toaS78s4QDx_Y",
    "https://madlads-collection.s3.us-west-2.amazonaws.com/_collection.png",
    "https://bafkreibtf35tniaqma43kvn5upi2e4qlroid56jxfm3nqtwjldrzaxrgtu.ipfs.nftstorage.link",
    "https://swpek5eflkanudn4ryibnapba6s7fh4kcumlk7oe4tln6zmbbnwq.arweave.net/lZ5FdIVagNoNvI4QFoHhB6Xyn4oVGLV9xOTW32WBC20",
    "https://prod-tensor-creators-s3.s3.us-east-1.amazonaws.com/collection/afb9fd81-75df-49e6-85a7-5b89567d70e3/images/585df3b2-bf27-4525-bab2-e0ed42098127",
  ];
  let currentIndex = 0;

  onMount(() => {
    setInterval(() => {
      currentIndex = (currentIndex + 1) % sliderImages.length;
    }, 1000);
  });
</script>

<div
  class="grid grid-cols-1 md:grid-cols-2 gap-8 items-center min-h-screen p-8"
>
  <div class="p-8">
    <div
      class="bg-opacity-20 rounded-3xl p-8 bg-gradient-to-br from-indigo-800 to-indigo-400 text-white opacity-80"
    >
      <div class="flex items-center mb-4">
        <h1 class="text-3xl font-bold text-white">Mint da Monke</h1>
      </div>
      <form on:submit|preventDefault={submitForm}>
        <div class="mb-4">
          <label
            for="nftName"
            class="block text-sm font-medium text-gray-700 dark:text-gray-300"
            >NFT Name</label
          >
          <input
            type="text"
            id="nftName"
            bind:value={nftName}
            class="mt-1 p-2 block w-full rounded-md border-2 text-black"
          />
        </div>
        <div class="mb-4">
          <label
            for="nftDescription"
            class="block text-sm font-medium text-gray-700 dark:text-gray-300"
            >NFT Description</label
          >
          <textarea
            id="nftDescription"
            bind:value={nftDescription}
            rows="4"
            class="mt-1 p-2 block w-full rounded-md border-2 text-black"
          />
        </div>
        <div class="mb-4">
          <label
            for="solanaAddresses"
            class="block text-sm font-medium text-gray-700 dark:text-gray-300"
            >Solana Addresses (comma-separated)</label
          >
          <input
            type="text"
            id="solanaAddresses"
            bind:value={solanaAddresses}
            class="mt-1 p-2 block w-full rounded-md border-2 text-black"
          />
        </div>
        <div class="mb-4">
          <label
            for="imageInput"
            class="block text-sm font-medium text-gray-700 dark:text-gray-300"
            >Image Input</label
          >
          <input
            type="file"
            id="imageInput"
            bind:this={imageInput}
            class="mt-1 p-2 block w-full rounded-md border-2 focus:border-blue-500 dark:border-gray-600"
          />
        </div>
        <button
          type="submit"
          class="w-full py-2 px-4 bg-blue-500 text-white rounded-md hover:bg-blue-700 focus:outline-none focus:ring focus:border-blue-300"
        >
          {#if loading}
            <div
              class="w-6 h-6 border-t-2 border-blue-500 border-solid rounded-full animate-spin"
            />
          {:else}
            Submit
          {/if}
        </button>
      </form>
    </div>
  </div>

  <div
    class="p-24 rounded-3xl bg-gradient-to-br from-purple-800 to-purple-400 text-white"
  >
    <div class="w-full h-full flex items-center overflow-hidden relative">
      {#each sliderImages as image (image)}
        <img
          src={image}
          alt="Slider Image"
          class="w-96 h-2/3 object-cover rounded-2xl transition-transform transform duration-500 ease-in-out p-2"
          style="transform: translateX(-{currentIndex * 100}%)"
        />
      {/each}
    </div>
  </div>
</div>
