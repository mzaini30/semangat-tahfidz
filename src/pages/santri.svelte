<script>
  import { push } from "svelte-spa-router";
  let santri = $state([]);
  async function init() {
    santri = JSON.parse(localStorage.getItem("santri")) || [];
  }
  init();
  async function simpan(event) {
    event.preventDefault();
    let terpilih = santri.filter((x) => x.nama != "");
    let menyimpan = localStorage.setItem("santri", JSON.stringify(terpilih));
    santri = [];
    push("/");
  }
  function tambah(event) {
    event.preventDefault();
    santri[crypto.randomUUID()] = {
      nama: "",
    };
    // santri = [
    //   ...santri,
    //   {
    //     id: crypto.randomUUID(),
    //     nama: "",
    //   },
    // ];
  }
</script>

<div class="container py-3">
  <h1 class="text-center uppercase mb-3">Santri</h1>
  <form action="" onsubmit={simpan} class="space-y-3">
    {#each santri as s}
      <div class="">
        <input
          type="text"
          bind:value={s.nama}
          placeholder="Masukkan nama santri"
          class="form-control"
        />
      </div>
    {/each}
    <div class="flex justify-between">
      <button class="btn btn-info" onclick={tambah}>+ Tambah</button>
      <input class="btn btn-success" type="submit" value="Simpan" />
    </div>
  </form>
</div>
