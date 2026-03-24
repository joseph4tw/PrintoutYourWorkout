# PrintoutYourWorkout — Claude Context

## Project Summary

A no-backend workout builder. Users select exercises, sets/reps, and days, then add them to a weekly plan. The full workout is serialized, gzip-compressed with `pako`, and encoded into the URL as a query param (`?workout=...`) so workouts can be shared or bookmarked with no server required.

Live at: [printoutyourworkout.com](https://www.printoutyourworkout.com/)

## Tech Stack

- **Framework:** Svelte 5 (running in legacy/Svelte 4 compatibility mode — uses `export let`, `on:event`, `{#each}`, etc. NOT runes)
- **Build tool:** Vite
- **Compression:** pako (`pako.deflate` / `pako.inflate`)
- **CSS:** Bootstrap 5 via CDN (loaded in `index.html`), plus Bootstrap Icons (`bi-*` classes)
- **Deployment:** AWS Amplify — auto-deploys on every push to `main`

## File Structure

```
src/
├── App.svelte          # Root component — renders <Form /> and a footer
├── app.css             # Global CSS — dark/light mode base styles
├── main.js             # Vite entry point
└── lib/
    ├── Form.svelte     # Primary UI — exercise picker, day selection, add button,
    │                     edit/share controls, and workout state management
    ├── WorkoutDay.svelte  # Renders one day's exercise table with edit controls
    ├── Print.svelte    # Print button (shows only when workout has items)
    └── exercises.json  # Static list of exercise names for the datalist autocomplete
```

## State & Data Flow

All state lives in `Form.svelte`. `WorkoutDay.svelte` and `Print.svelte` are presentational.

### `workoutItems` array

The central data structure — a sparse array indexed by day (0=Monday … 6=Sunday):

```js
workoutItems[0] = {
  day: "Monday",
  exercises: [
    { id: 1234567890, name: "Bench Press", sets: 3, reps: 10 },
    ...
  ]
}
```

- A slot is `undefined` if no exercises are planned for that day.
- `workoutItems = workoutItems` is the Svelte legacy pattern used to force reactive updates after array mutations (splice, etc.).

### Event callbacks (prop functions)

`WorkoutDay` receives `onRemoveExercise` and `onMoveExercise` as props (function refs). It calls them with `{ day, id }` or `{ day, id, direction }` objects. Handlers live in `Form.svelte`.

## Key Patterns

### URL serialization

```js
// Serialize
const json = JSON.stringify(workoutItems);
const compressed = pako.deflate(new TextEncoder().encode(json));
const encoded = btoa(binaryString); // base64

// Deserialize (on mount)
const decompressed = pako.inflate(compressedBytes);
const workoutItems = JSON.parse(new TextDecoder().decode(decompressed));
```

### Temporary success states

Used for "Link Copied!" and "Added!" feedback:
```js
let copySuccess = false;
// ...
copySuccess = true;
setTimeout(() => { copySuccess = false; }, 2000);
```
The button swaps class and label based on the boolean.

### Delete confirmation flow (WorkoutDay.svelte)

Two-click confirmation — no browser `confirm()` dialogs:
1. First click sets `pendingDeleteId = exercise.id` → icon changes from `bi-x-circle` to `bi-check-circle-fill`
2. Second click adds id to `fadingIds` → CSS fade-out animation plays
3. `animationend` fires → `onRemoveExercise` is called to remove from state
4. Auto-resets after 3 seconds if not confirmed

`fadingIds` is a `Set`; always reassign (`fadingIds = new Set([...fadingIds, id])`) to trigger Svelte reactivity.

### Print layout

Bootstrap's `@media print` utilities + `.no-print` class hide all UI chrome on print. `WorkoutDay` uses `page-break-inside: avoid` to keep each day's table together.

## Development

```bash
npm install
npm run dev      # Vite dev server
npm run build    # Production build
npm run preview  # Preview production build locally
```

## Conventions

- No backend, no auth, no database — keep it that way.
- Don't add npm packages for UI interactions — use vanilla HTML/CSS/JS within Svelte.
- Keep Svelte in legacy mode (Svelte 4 syntax). Don't migrate to runes unless explicitly asked.
- Bootstrap classes for layout/components; scoped `<style>` blocks in `.svelte` files for component-specific styles.
- Exercise IDs are `Date.now()` timestamps — simple and collision-free for this scale.
