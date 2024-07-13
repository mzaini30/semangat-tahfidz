<script>
  let { params } = $props();
  let datanya = $state([]);
  let santri = $state({});
  datanya = JSON.parse(localStorage.getItem("data")) || [];
  santri = JSON.parse(localStorage.getItem("santri")) || [];
  santri = santri.filter((x) => x.id == params.idSantri)[0];
  datanya = datanya.filter((x) => x.idSantri == params.idSantri);
  console.log(datanya);
</script>

<div class="container py-3">
  <h1>{santri.nama}</h1>
  <table class="table">
    <thead>
      <tr>
        <th>Tanggal</th>
        <th>Status</th>
      </tr>
    </thead>
    <tbody>
      {#each datanya as d}
        <tr>
          <td>{d.tanggal}</td>
          <td
            >{d.status
              .replace("baru_mulai", "Ziyadah (Persiapan)")
              .replace("baru_selesai", "Ziyadah")
              .replace("juziyah_mulai", "Juziyah (Persiapan)")
              .replace("juziyah_selesai", "Juziyah")
              .replace("syahadah_mulai", "Syahadah (Persiapan)")
              .replace("syahadah_selesai", "Syahadah")}</td
          >
        </tr>
      {/each}
    </tbody>
  </table>
</div>
