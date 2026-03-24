<script>
  export let day;
  export let exercises;
  export let includeNotes;
  export let inEditMode;
  export let onRemoveExercise;
  export let onMoveExercise;

  const maxSets = exercises.reduce((prev, cur) => Math.max(cur.sets, prev), 0);

  let pendingDeleteId = null;
  let fadingIds = new Set();
  let pendingDeleteTimeout = null;

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

  function handleDeleteClick(day, id) {
    if (pendingDeleteId === id) {
      // Second click — start the fade, then delete
      fadingIds = new Set([...fadingIds, id]);
      pendingDeleteId = null;
      clearTimeout(pendingDeleteTimeout);
    } else {
      // First click — enter pending/confirm state
      clearTimeout(pendingDeleteTimeout);
      pendingDeleteId = id;
      pendingDeleteTimeout = setTimeout(() => {
        pendingDeleteId = null;
      }, 3000);
    }
  }

  function handleAnimationEnd(day, id, event) {
    // Guard: only fire on the tr's own animation, not bubbled child events
    if (event.target !== event.currentTarget) return;
    if (fadingIds.has(id)) {
      fadingIds.delete(id);
      fadingIds = fadingIds;
      removeExercise(day, id);
    }
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
            <tr
              class:exercise-fading={fadingIds.has(exercise.id)}
              on:animationend={(e) => handleAnimationEnd(day, exercise.id, e)}
            >
              <th class="align-middle" scope="row">
                {#if inEditMode}
                  <a
                    href="#_"
                    class="me-1 fs-5 text-decoration-none text-danger no-print"
                    aria-label="Remove exercise"
                    on:click|preventDefault={() => handleDeleteClick(day, exercise.id)}
                  >
                    {#if pendingDeleteId === exercise.id}
                      <i class="bi bi-check-circle-fill"></i>
                    {:else}
                      <i class="bi bi-x-circle"></i>
                    {/if}
                  </a>
                  <a
                    href="#_"
                    class="me-1 fs-5 text-decoration-none text-primary no-print"
                    aria-label="Move exercise up"
                    on:click|preventDefault={() => moveExercise(day, exercise.id, 'up')}
                  >
                    <i class="bi bi-arrow-up-circle"></i>
                  </a>
                  <a
                    href="#_"
                    class="me-1 fs-5 text-decoration-none text-primary no-print"
                    aria-label="Move exercise down"
                    on:click|preventDefault={() => moveExercise(day, exercise.id, 'down')}
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

  @keyframes exerciseFadeOut {
    from {
      opacity: 1;
      transform: translateX(0);
    }
    to {
      opacity: 0;
      transform: translateX(-12px);
    }
  }

  tr.exercise-fading {
    animation: exerciseFadeOut 0.35s ease-out forwards;
    pointer-events: none;
  }

  @media print {
    .no-print {
      display: none;
    }
  }
</style>
