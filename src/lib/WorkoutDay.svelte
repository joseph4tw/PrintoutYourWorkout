<script>
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher();

  export let day;
  export let exercises;
  export let includeNotes;
  export let inEditMode;

  const maxSets = exercises.reduce((prev, cur) => Math.max(cur.sets, prev), 0);

  function removeExercise(day, id) {
    dispatch("removeExercise", {
      day,
      id,
    });
  }
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
              <th scope="row">
                {#if inEditMode}
                  <a
                    href="#"
                    class="text-decoration-none text-danger no-print"
                    on:click={() => removeExercise(day, exercise.id)}
                  >
                    <i class="bi bi-x-circle" />
                  </a>
                {/if}
                {exercise.name}:
                <small class="text-secondary"><em>{exercise.sets} sets of {exercise.reps} reps</em></small>
              </th>
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

  {#if includeNotes}
    <div class="row">
      <div class="col">
        <strong>Notes:</strong>
        <hr class="mt-0 mb-4" />
        <hr class="mb-4" />
        <hr class="mb-4" />
      </div>
    </div>
  {/if}
</section>

<style>
  section {
    page-break-inside: avoid;
  }

  @media print {
    .no-print {
      display: none;
    }
  }
</style>
