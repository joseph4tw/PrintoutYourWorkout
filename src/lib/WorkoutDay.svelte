<script>
  export let day;
  export let exercises;
  export let includeNotes;
  export let inEditMode;
  export let onRemoveExercise;
  export let onMoveExercise;

  const maxSets = exercises.reduce((prev, cur) => Math.max(cur.sets, prev), 0);

  function removeExercise(day, id) {
    onRemoveExercise?.({
      day,
      id,
    });
  }

  function moveExercise(day, id, direction) {
    onMoveExercise?.({
      day,
      id,
      direction,
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
              <th class="align-middle" scope="row">
                {#if inEditMode}
                  <a
                    href="#_"
                    class="me-1 fs-5 text-decoration-none text-danger no-print"
                    aria-label="Remove exercise"
                    on:click={() => removeExercise(day, exercise.id)}
                  >
                    <i class="bi bi-x-circle"></i>
                  </a>
                  <a
                    href="#_"
                    class="me-1 fs-5 text-decoration-none text-primary no-print"
                    aria-label="Move exercise up"
                    on:click={() => moveExercise(day, exercise.id, 'up')}
                  >
                    <i class="bi bi-arrow-up-circle"></i>
                  </a>
                  <a
                    href="#_"
                    class="me-1 fs-5 text-decoration-none text-primary no-print"
                    aria-label="Move exercise down"
                    on:click={() => moveExercise(day, exercise.id, 'down')}
                  >
                    <i class="bi bi-arrow-down-circle"></i>
                  </a>
                {/if}
                {exercise.name}:
                <small class="text-secondary"><em>{exercise.sets} sets of {exercise.reps} reps</em></small>
              </th>
              {#each Array(maxSets) as _, index}
                {#if index < exercise.sets}
                  <td class="text-center align-middle">x</td>
                {:else}
                  <td></td>
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
