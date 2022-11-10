<script>
  import Form from './lib/Form.svelte';
</script>

<main>
  <h1 class="no-print">Printout Your Workout</h1>
  <p class="no-print">A simple tool to create your own printable workout plan.</p>

  <Form/>
</main>

<style>
  @media print {
    .no-print {
      display: none;
    }
  }
</style>
