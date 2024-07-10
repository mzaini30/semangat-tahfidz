<script>
  import Trophy from "../icon/trophy.svelte";
  import Jam from "../icon/jam.svelte";
  let santri = $state([]);
  santri = JSON.parse(localStorage.getItem("santri")) || [];
  let data = $state([]);
  data = JSON.parse(localStorage.getItem("data")) || [];
</script>

<div class="container py-3">
  <div class="flex justify-between mb-3 items-center">
    <h1 class="">Semangat Tahfidz</h1>
    <div>
      <a href="#/santri" class="btn btn-info">+ Santri</a>
    </div>
  </div>
  <a href="#/konsep" class="w-full btn btn-success text-center uppercase mb-3"
    >Konsep</a
  >
  {#if santri}
    <div class="space-y-3">
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
                  <th>PJ</th>
                  <th>PS</th>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <td><Trophy></Trophy></td>
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
                  <td></td>
                  <td><input type="checkbox" /></td>
                  <td><input type="checkbox" /></td>
                  <td><input type="checkbox" /></td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      {/each}
    </div>
  {/if}
</div>
