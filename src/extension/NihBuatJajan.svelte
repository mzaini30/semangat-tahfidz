<script>
  import { acak } from "kumpulan-tools";
  import { fade } from "svelte/transition";
  const { username } = $props();
  let donasi = [];
  let tampil = $state("");
  let triggerTampil = $state(false);
  let intervalnya;
  async function dapatkan() {
    let halaman = await fetch(
      `https://cors.mabaiz.web.id/?link=https://www.nihbuatjajan.com/${username}`,
    );
    halaman = await halaman.text();
    let domHalaman = new DOMParser().parseFromString(halaman, "text/html");
    let donation = domHalaman.querySelectorAll(".donation");
    if (donation.length > 0) {
      for (let x of donation) {
        donasi = [...donasi, x.querySelector(".donator").innerHTML.trim()];
      }
      intervalnya = setInterval(() => {
        tampil = acak(donasi)[0];
        triggerTampil = true;
        setTimeout(() => (triggerTampil = false), 5 * 1000);
      }, 20 * 1000);
    }
  }
  dapatkan();
  function dapatkanUlang() {
    clearInterval(intervalnya);
    dapatkan();
  }
</script>

{#if triggerTampil}
  <div
    class="fixed top-0 left-0 md:bottom-2 md:!top-auto md:left-2 w-full md:w-min z-[999]"
    out:fade
  >
    <div
      class="alert alert-success md:mb-0 flex justify-between md:space-x-3"
      role="alert"
    >
      <div class="whitespace-nowrap [&_.dialog]:hidden">
        {@html tampil}
      </div>
      <button
        onclick={() => (triggerTampil = false)}
        type="button"
        class="btn-close"
        data-bs-dismiss="alert"
        aria-label="Close"
      ></button>
    </div>
  </div>
{/if}
<a
  onclick={dapatkanUlang}
  href="https://www.nihbuatjajan.com/{username}"
  class="fixed bottom-2 right-2 btn btn-primary z-[999]"
  target="_blank">Support</a
>
