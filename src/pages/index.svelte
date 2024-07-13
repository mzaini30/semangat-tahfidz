<script>
  import Trophy from "../icon/trophy.svelte";
  import Jam from "../icon/jam.svelte";
  import { getFormattedDate } from "../function";
  import collect from "collect.js";
  import { watch } from "runed";

  let sekarang = getFormattedDate(new Date());
  // let sekarang = "2024-07-16"; // TODO: jangan lupa dikembalikan

  let santri = $state([]);
  santri = JSON.parse(localStorage.getItem("santri")) || [];
  console.log(santri);

  let data = $state([]);
  data = JSON.parse(localStorage.getItem("data")) || [];

  let kesimpulan = $state([]);
  function generateStatistik() {
    let dataKesimpulan = [];
    kesimpulan = [];
    dataKesimpulan = JSON.parse(localStorage.getItem("data")) || [];
    dataKesimpulan = dataKesimpulan.filter((x) => x.status != "libur");
    // kesimpulan = collect(kesimpulan).groupBy("idSantri").all();
    for (let x of santri) {
      let datanya = dataKesimpulan.filter((y) => y.idSantri == x.id);
      console.log(datanya);
      let terkumpul = {
        baru: [],
        juziyah: [],
        syahadah: [],
      };
      let indexBaru = 0;
      let indexJuziyah = 0;
      let indexSyahadah = 0;
      for (let n in datanya) {
        if (
          datanya[n].status == "baru_mulai" ||
          datanya[n].status == "baru_selesai"
        ) {
          if (+n == 0) {
            terkumpul.baru[indexBaru] = 1;
          } else if (
            datanya[+n - 1].status == "baru_mulai"
            //   ||
            // datanya[+n - 1].status == "baru_selesai"
          ) {
            terkumpul.baru[indexBaru] += 1;
          } else if (
            datanya[+n - 1].status != "baru_mulai"
            //   &&
            // datanya[+n - 1].status != "baru_selesai"
          ) {
            indexBaru += 1;
            terkumpul.baru[indexBaru] = 1;
          }
        }

        if (
          datanya[n].status == "juziyah_mulai" ||
          datanya[n].status == "juziyah_selesai"
        ) {
          if (+n == 0) {
            terkumpul.juziyah[indexJuziyah] = 1;
          } else if (
            datanya[+n - 1].status == "juziyah_mulai" ||
            datanya[+n - 1].status == "juziyah_selesai"
          ) {
            terkumpul.juziyah[indexJuziyah] += 1;
          } else if (
            datanya[+n - 1].status != "juziyah_mulai" &&
            datanya[+n - 1].status != "juziyah_selesai"
          ) {
            indexJuziyah += 1;
            terkumpul.juziyah[indexJuziyah] = 1;
          }
        }

        if (
          datanya[n].status == "syahadah_mulai" ||
          datanya[n].status == "syahadah_selesai"
        ) {
          if (+n == 0) {
            terkumpul.syahadah[indexSyahadah] = 1;
          } else if (
            datanya[+n - 1].status == "syahadah_mulai" ||
            datanya[+n - 1].status == "syahadah_selesai"
          ) {
            terkumpul.syahadah[indexSyahadah] += 1;
          } else if (
            datanya[+n - 1].status != "syahadah_mulai" &&
            datanya[+n - 1].status != "syahadah_selesai"
          ) {
            indexSyahadah += 1;
            terkumpul.syahadah[indexSyahadah] = 1;
          }
        }
      }
      console.log(terkumpul);
      kesimpulan.push({
        id: crypto.randomUUID(),
        idSantri: x.id,
        baru: terkumpul.baru.filter((x) => x),
        juziyah: terkumpul.juziyah.filter((x) => x),
        syahadah: terkumpul.syahadah.filter((x) => x),
        baruTerakhir: terkumpul.baru.filter((x) => x).at(-1),
        juziyahTerakhir: terkumpul.juziyah.filter((x) => x).at(-1),
        syahadahTerakhir: terkumpul.syahadah.filter((x) => x).at(-1),
        baruTerkecil: Math.min(...terkumpul.baru.filter((x) => x)),
        juziyahTerkecil: Math.min(...terkumpul.juziyah.filter((x) => x)),
        syahadahTerkecil: Math.min(...terkumpul.syahadah.filter((x) => x)),
        baruMerah:
          Math.min(...terkumpul.baru.filter((x) => x)) <
          terkumpul.baru.filter((x) => x).at(-1),
        juziyahMerah:
          Math.min(...terkumpul.juziyah.filter((x) => x)) <
          terkumpul.juziyah.filter((x) => x).at(-1),
        syahadahMerah:
          Math.min(...terkumpul.syahadah.filter((x) => x)) <
          terkumpul.syahadah.filter((x) => x).at(-1),
      });
    }
    console.log(JSON.stringify(dataKesimpulan));
    console.log(JSON.stringify(kesimpulan));
  }
  generateStatistik();

  let statusTanpaLibur = $state([]);

  for (let s of santri) {
    let tanpaLibur = data.filter(
      (x) => x.status != "libur" && x.idSantri == s.id,
    );
    tanpaLibur = tanpaLibur[tanpaLibur.length - 1];
    console.log(tanpaLibur);
    statusTanpaLibur.push(tanpaLibur);
  }

  let terakhir = data.filter((x) => x.tanggal == sekarang);
  if (terakhir.length == 0) {
    for (let s of santri) {
      let status = "baru_mulai";
      let statusSebelumnya = statusTanpaLibur.filter(
        (x) => x?.idSantri == s.id,
      )[0]?.status;
      if (
        statusSebelumnya == "juziyah_mulai" ||
        statusSebelumnya == "syahadah_mulai"
      ) {
        status = statusSebelumnya;
      }
      // let status =
      //   statusTanpaLibur.filter((x) => x?.idSantri == s.id)[0]?.status ||
      //   "baru_mulai";
      terakhir.push({
        id: crypto.randomUUID(),
        idSantri: s.id,
        tanggal: sekarang,
        status,
      });
      localStorage.setItem("data", JSON.stringify(data));
    }
    console.log(statusTanpaLibur);
  }

  function jadiTanpaLiburSemua() {
    for (let n in untukRadio) {
      let status = statusTanpaLibur.filter((x) => x.idSantri == n)[0];
      console.log(status);
      untukRadio[n].status = status?.status;
    }
  }

  let untukRadio = $state({});

  for (let x of terakhir) {
    untukRadio[x.idSantri] = {
      id: x.id,
      tanggal: x.tanggal,
      status: x.status,
    };
  }

  for (let n in untukRadio) {
    watch(
      () => untukRadio[n].status,
      () => {
        console.log(JSON.stringify(untukRadio));
        for (let n in untukRadio) {
          data = data.filter((x) => x.id != untukRadio[n].id);
          data.push({
            id: untukRadio[n].id,
            tanggal: untukRadio[n].tanggal,
            status: untukRadio[n].status,
            idSantri: n,
          });
        }
        localStorage.setItem("data", JSON.stringify(data));
        generateStatistik();
      },
    );
  }

  function jadiLiburSemua() {
    for (let n in untukRadio) {
      untukRadio[n].status = "libur";
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
    <button onclick={jadiTanpaLiburSemua} class="btn btn-success uppercase"
      >Masuk</button
    >
    <button onclick={jadiLiburSemua} class="btn btn-danger uppercase"
      >Libur</button
    >
  </div>
  {#if santri}
    <div class="grid gap-3 md:grid-cols-4">
      {#each santri as s}
        <div class="list-group">
          <div class="list-group-item active">
            <div class="uppercase font-bold flex items-center justify-between">
              <div>
                {s.nama}
              </div>
              <a href="#/history/{s.id}" class="btn btn-info text-sm">History</a
              >
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
                  <td>
                    {#if kesimpulan.filter((x) => x.idSantri == s.id)[0].baruTerakhir}
                      {kesimpulan.filter((x) => x.idSantri == s.id)[0]
                        .baruTerkecil - 1} hari
                    {/if}
                  </td>
                  <td>
                    {#if kesimpulan.filter((x) => x.idSantri == s.id)[0].juziyahTerakhir}
                      {kesimpulan.filter((x) => x.idSantri == s.id)[0]
                        .juziyahTerkecil - 1} hari
                    {/if}
                  </td>
                  <td>
                    {#if kesimpulan.filter((x) => x.idSantri == s.id)[0].syahadahTerakhir}
                      {kesimpulan.filter((x) => x.idSantri == s.id)[0]
                        .syahadahTerkecil - 1} hari
                    {/if}
                  </td>
                </tr>
                <tr>
                  <td><Jam></Jam></td>
                  <!-- <td class="!bg-red-500 !text-white">10 hari</td> -->
                  <td
                    class={kesimpulan.filter((x) => x.idSantri == s.id)[0]
                      .baruMerah
                      ? "!bg-red-500 !text-white"
                      : null}
                  >
                    {#if kesimpulan.filter((x) => x.idSantri == s.id)[0].baruTerakhir}
                      {kesimpulan.filter((x) => x.idSantri == s.id)[0]
                        .baruTerakhir - 1} hari
                    {/if}
                  </td>
                  <td
                    class={kesimpulan.filter((x) => x.idSantri == s.id)[0]
                      .juziyahMerah
                      ? "!bg-red-500 !text-white"
                      : null}
                  >
                    {#if kesimpulan.filter((x) => x.idSantri == s.id)[0].juziyahTerakhir}
                      {kesimpulan.filter((x) => x.idSantri == s.id)[0]
                        .juziyahTerakhir - 1} hari
                    {/if}
                  </td>
                  <td
                    class={kesimpulan.filter((x) => x.idSantri == s.id)[0]
                      .syahadahMerah
                      ? "!bg-red-500 !text-white"
                      : null}
                  >
                    {#if kesimpulan.filter((x) => x.idSantri == s.id)[0].syahadahTerakhir}
                      {kesimpulan.filter((x) => x.idSantri == s.id)[0]
                        .syahadahTerakhir - 1} hari
                    {/if}
                  </td>
                </tr>
                <tr>
                  <td>Persiapan</td>
                  <td
                    ><input
                      type="radio"
                      bind:group={untukRadio[s.id].status}
                      value="baru_mulai"
                    /></td
                  >
                  <td
                    ><input
                      type="radio"
                      bind:group={untukRadio[s.id].status}
                      value="juziyah_mulai"
                    /></td
                  >
                  <td
                    ><input
                      type="radio"
                      bind:group={untukRadio[s.id].status}
                      value="syahadah_mulai"
                    /></td
                  >
                </tr>
                <tr>
                  <td>Sudah</td>
                  <td
                    ><input
                      type="radio"
                      bind:group={untukRadio[s.id].status}
                      value="baru_selesai"
                    /></td
                  >
                  <td
                    ><input
                      type="radio"
                      bind:group={untukRadio[s.id].status}
                      value="juziyah_selesai"
                    /></td
                  >
                  <td
                    ><input
                      type="radio"
                      bind:group={untukRadio[s.id].status}
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
                      bind:group={untukRadio[s.id].status}
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
