<script>
  import Trophy from "../icon/trophy.svelte";
  import Jam from "../icon/jam.svelte";
  import { getFormattedDate } from "../function";
  let sekarang = getFormattedDate(new Date());
  let santri = $state([]);
  santri = JSON.parse(localStorage.getItem("santri")) || [];
  let data = $state([]);
  data = JSON.parse(localStorage.getItem("data")) || [];
  // dummy
  for (let s of santri) {
    data = [
      ...data,
      {
        id: crypto.randomUUID(),
        idSantri: s.id,
        tanggal: "2024-01-01",
        status: "baru_mulai",
      },
      {
        id: crypto.randomUUID(),
        idSantri: s.id,
        tanggal: getFormattedDate(new Date()),
        status: "baru_mulai",
      },
    ];
  }
  // /dummy
  console.log(JSON.stringify(data));
  let dataTerpilih = data.filter((x) => x.tanggal == sekarang);
  console.log(JSON.stringify(dataTerpilih));
  let untukRadio = $state({});
  for (let x of dataTerpilih) {
    untukRadio[x.idSantri] = x.status;
  }
  console.log(JSON.stringify(untukRadio));
  $effect(() => {
    if (untukRadio) {
      console.log(JSON.stringify(untukRadio));
    }
  });
  function jadiLiburSemua() {
    for (let n in untukRadio) {
      untukRadio[n] = "libur";
    }
  }
</script>

<div class="container py-3">
  <div class="flex justify-between mb-3 items-center">
    <h1 class="">Semangat Tahfidz</h1>
    <div>
      <a href="#/santri" class="btn btn-info">+ Santri</a>
    </div>
  </div>
  <div class="grid grid-cols-3 gap-3 mb-3 text-center">
    <a href="#/konsep" class="btn btn-primary uppercase">Konsep</a>
    <button class="btn btn-success uppercase">Masuk</button>
    <button onclick={jadiLiburSemua} class="btn btn-danger uppercase"
      >Libur</button
    >
  </div>
  {#if santri}
    <div class="grid gap-3 md:grid-cols-4">
      {#each santri as s}
        <div class="list-group">
          <div class="list-group-item active">
            <div class="uppercase font-bold">
              {s.nama}
            </div>
          </div>
          <div class="list-group-item">
            <table class="table text-center text-sm">
              <thead>
                <tr>
                  <th></th>
                  <th>Baru</th>
                  <th>Juziyah</th>
                  <th>Syahadah</th>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <td class="w-13%"><Trophy></Trophy></td>
                  <td>3 hari</td>
                  <td>5 hari</td>
                  <td>8 hari</td>
                </tr>
                <tr>
                  <td><Jam></Jam></td>
                  <td class="!bg-red-500 !text-white">10 hari</td>
                  <td>5 hari</td>
                  <td>8 hari</td>
                </tr>
                <tr>
                  <td>Mulai</td>
                  <td
                    ><input
                      type="radio"
                      bind:group={untukRadio[s.id]}
                      value="baru_mulai"
                    /></td
                  >
                  <td
                    ><input
                      type="radio"
                      bind:group={untukRadio[s.id]}
                      value="juziyah_mulai"
                    /></td
                  >
                  <td
                    ><input
                      type="radio"
                      bind:group={untukRadio[s.id]}
                      value="syahadah_mulai"
                    /></td
                  >
                </tr>
                <tr>
                  <td>Selesai</td>
                  <td
                    ><input
                      type="radio"
                      bind:group={untukRadio[s.id]}
                      value="baru_selesai"
                    /></td
                  >
                  <td
                    ><input
                      type="radio"
                      bind:group={untukRadio[s.id]}
                      value="juziyah_selesai"
                    /></td
                  >
                  <td
                    ><input
                      type="radio"
                      bind:group={untukRadio[s.id]}
                      value="syahadah_selesai"
                    /></td
                  >
                </tr>
                <tr>
                  <td>Libur</td>
                  <td
                    ><input
                      type="radio"
                      disabled
                      bind:group={untukRadio[s.id]}
                      value="libur"
                    /></td
                  >
                  <td></td>
                  <td></td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      {/each}
    </div>
  {/if}
</div>
