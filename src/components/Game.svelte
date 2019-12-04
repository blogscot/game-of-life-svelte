<script>
  import { onMount } from "svelte";
  import MediaQuery from "svelte-media-query";
  import Grid from "./Grid.svelte";

  let columns = 300;
  let rows = 300;
  let size = 2;
  let grid = buildNewGrid();

  function buildNewGrid() {
    return Array(columns)
      .fill(false)
      .map(x => Array(rows).fill(false));
  }
  function iterator(func) {
    for (let column = 0; column < columns; column++) {
      for (let row = 0; row < rows; row++) {
        func(row, column);
      }
    }
  }
  function addStartingPopulation() {
    iterator((row, column) => {
      if (Math.random() < 0.1) {
        grid[column][row] = true;
      }
    });
  }
  function isCellAlive(x, y) {
    const adjacentCellCount = getAdjacentCount(x, y);
    const isAlive = grid[x][y];
    const countIsTwo = adjacentCellCount === 2;
    const countIsThree = adjacentCellCount === 3;
    return (
      ((countIsTwo || countIsThree) && isAlive) || (countIsThree && !isAlive)
    );
  }
  function getAdjacentCount(x, y) {
    return getNeighbourCells(x, y).filter(([x, y]) => grid[x][y]).length;
  }
  function getNeighbourCells(x, y) {
    const offsets = [
      [-1, -1],
      [0, -1],
      [1, -1],
      [1, 0],
      [1, 1],
      [0, 1],
      [-1, 1],
      [-1, 0]
    ];
    return offsets.map(([dX, dY]) => [
      (x + dX + columns) % columns,
      (y + dY + rows) % rows
    ]);
  }
  function clearGrid() {
    iterator((row, column) => {
      if (grid[column][row]) {
        grid[column][row] = false;
      }
    });
  }
  function generateNextGeneration() {
    const newGrid = buildNewGrid();
    iterator((row, column) => {
      if (isCellAlive(column, row)) {
        newGrid[column][row] = true;
      }
    });
    return newGrid;
  }
  function run() {
    const newGrid = generateNextGeneration();
    clearGrid();
    grid = newGrid;
    requestAnimationFrame(run);
  }
  onMount(() => {
    addStartingPopulation();
    // Allow a pause for first paint to render
    setTimeout(run, 500);
  });
</script>

<style>
  section {
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  main {
    display: grid;
    justify-content: center;
    grid-template-columns: repeat(var(--columns), var(--size));
    overflow: hidden;
  }

  h1 {
    text-align: center;
    margin-bottom: 2em;
  }
</style>

<section>
  <h1>Game of Life</h1>
  <MediaQuery query="(max-width: 480px)" let:matches>
    {#if matches}
      <main style="--size:{1}px; --columns:{columns};">
        <Grid {grid} />
      </main>
    {/if}
  </MediaQuery>

  <MediaQuery query="(min-width: 481px)" let:matches>
    {#if matches}
      <main style="--size:{size}px; --columns:{columns};">
        <Grid {grid} />
      </main>
    {/if}
  </MediaQuery>
</section>
