<script>
  import WorkoutDay from "./WorkoutDay.svelte";
  import Print from "./Print.svelte";

  import exercises from "./exercises.json";
  let reps = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20];
  let sets = [1, 2, 3, 4, 5];
  let workoutItems = [];
  let selectedExercise, selectedSets, selectedReps;
  let onMonday, onTuesday, onWednesday, onThursday, onFriday, onSaturday, onSunday;
  let includeNotes = true;

  const addWorkoutItem = () => {
    const exercise = {
      name: selectedExercise,
      sets: Number(selectedSets),
      reps: Number(selectedReps),
    };

    const addExercise = (day, index, exercise) => {
      if (!workoutItems[index]) {
        workoutItems[index] = {};
      }

      if (!workoutItems[index].exercises) {
        workoutItems[index].exercises = [];
      }

      workoutItems[index] = {
        day,
        exercises: [...workoutItems[index].exercises, {...exercise}],
      };
    };

    onMonday && addExercise("Monday", 0, exercise);
    onTuesday && addExercise("Tuesday", 1, exercise);
    onWednesday && addExercise("Wednesday", 2, exercise);
    onThursday && addExercise("Thursday", 3, exercise);
    onFriday && addExercise("Friday", 4, exercise);
    onSaturday && addExercise("Saturday", 5, exercise);
    onSunday && addExercise("Sunday", 6, exercise);
  };
</script>

<div class="row no-print">
  <div class="col">
    <form>

      <div class="mb-3">
        <div class="col">
          <label for="exercise" class="form-label">Exercise</label>
          <input class="form-control" list="exerciseOptions" id="exercise" placeholder="Type to search..." bind:value={selectedExercise}>
          <datalist id="exerciseOptions">
            {#each exercises as exercise}
              <option value={exercise}>{exercise}</option>
            {/each}
          </datalist>
        </div>
      </div>

      <div class="row mb-3">
        <div class="col">
          <label for="sets" class="form-label">Sets</label>
          <select id="sets" class="form-select" aria-label="Sets" bind:value={selectedSets}>
            {#each sets as set}
              <option value={set}>{set}</option>
            {/each}
          </select>
        </div>

        <div class="col">
          <label for="reps" class="form-label">Reps</label>
          <select id="reps" class="form-select" aria-label="Reps" bind:value={selectedReps}>
            {#each reps as rep}
              <option value={rep}>{rep}</option>
            {/each}
          </select>
        </div>
      </div>

      <div class="row mb-3">
        <div class="col">
          <label for="days-of-the-week" class="form-label">Days of the Week</label>
          <div id="days-of-the-week" class="btn-group w-100" role="group" aria-label="Days of the week toggle group">
            <input type="checkbox" class="btn-check" id="mon" autocomplete="off" bind:checked={onMonday} />
            <label class="btn btn-outline-secondary" for="mon">Mon</label>

            <input type="checkbox" class="btn-check" id="tue" autocomplete="off" bind:checked={onTuesday} />
            <label class="btn btn-outline-secondary" for="tue">Tue</label>

            <input type="checkbox" class="btn-check" id="web" autocomplete="off" bind:checked={onWednesday} />
            <label class="btn btn-outline-secondary" for="web">Wed</label>

            <input type="checkbox" class="btn-check" id="thu" autocomplete="off" bind:checked={onThursday} />
            <label class="btn btn-outline-secondary" for="thu">Thu</label>

            <input type="checkbox" class="btn-check" id="fri" autocomplete="off" bind:checked={onFriday} />
            <label class="btn btn-outline-secondary" for="fri">Fri</label>

            <input type="checkbox" class="btn-check" id="sat" autocomplete="off" bind:checked={onSaturday} />
            <label class="btn btn-outline-secondary" for="sat">Sat</label>

            <input type="checkbox" class="btn-check" id="sun" autocomplete="off" bind:checked={onSunday} />
            <label class="btn btn-outline-secondary" for="sun">Sun</label>
          </div>
        </div>
      </div>

      <button type="button" class="btn btn-primary w-100" on:click={addWorkoutItem}>Add To Workout Plan</button>
    </form>
  </div>
</div>

<hr class="my-4 no-print" />

{#if workoutItems.length > 0}
<div class="row no-print">
  <div class="col">
    <form>

      <div class="row mb-3">
        <div class="col">
          <div class="form-check">
            <input class="form-check-input" type="checkbox" value="" id="include-notes" bind:checked={includeNotes}>
            <label class="form-check-label" for="include-notes">
              Include Notes
            </label>
          </div>
        </div>
      </div>

    </form>
  </div>
</div>
{/if}

{#each workoutItems as item}
  {#if item}
    <WorkoutDay day={item.day} exercises={item.exercises} includeNotes={includeNotes} />
  {/if}
{/each}

{#if workoutItems.length > 0}
  <Print />
{/if}

<style>
  @media print {
    .no-print {
      display: none;
    }
  }
</style>