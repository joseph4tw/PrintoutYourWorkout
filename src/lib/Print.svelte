<script>
  const handleClick = (e) => {
    e.preventDefault();
    e.stopPropagation();
    window.print();
  };
</script>

<button type="button" class="btn btn-secondary no-print" on:click={handleClick}>
    <i class="bi bi-printer me-2"></i> Print
</button>

<style>
  @media print {
    .no-print {
      display: none;
    }
  }
</style>
