<script>
  export let day;
  export let exercises;

  const maxSets = exercises.reduce((prev, cur) => Math.max(cur.sets, prev), 0);
</script>

<section>
<div class="row">
  <div class="col">
    <table class="table table-striped table-bordered">
      <thead>
        <tr class="table-secondary">
          <th scope="col" colspan={maxSets + 1}>{day}</th>
        </tr>
        <tr>
          <th scope="col" style="width: 60%;">Exercises</th>
          <th scope="col" colspan={maxSets} style="width: 40%;">Sets</th>
        </tr>
      </thead>
      <tbody>
        {#each exercises as exercise}
          <tr>
            <th scope="row"
              >{exercise.name}:
              <small class="text-secondary"><em>{exercise.sets} sets of {exercise.reps} reps</em></small></th
            >
            {#each Array(maxSets) as _, index}
              {#if index < exercise.sets}
                <td class="text-center">x</td>
              {:else}
                <td />
              {/if}
            {/each}
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
</div>
<div class="row">
  <div class="col">
    <strong>Notes:</strong>
    <hr class="mt-0 mb-4" />
    <hr class="mb-4" />
    <hr class="mb-4" />
  </div>
</div>
</section>

<style>
    section {
        page-break-inside: avoid;
    }
</style>