<script>
  export let grid;
</script>

<style>
  div {
    height: var(--size);
    width: var(--size);
  }
</style>

{#each [...grid] as column, c}
  {#each [...grid][0] as row, r}
    <div style="background: {grid[c][r] ? 'blue' : 'white'}" />
  {/each}
{/each}
