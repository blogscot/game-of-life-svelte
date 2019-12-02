<script>
  import { onMount } from "svelte";

  let columns = 300;
  let rows = 300;
  let size = 3;
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
    return (
      (isAlive && (adjacentCellCount === 2 || adjacentCellCount === 3)) ||
      (!isAlive && adjacentCellCount === 3)
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
    window.requestAnimationFrame(run);
  }
  onMount(() => {
    addStartingPopulation();
    run();
  });
</script>

<style>
  main {
    display: grid;
    grid-template-columns: repeat(var(--columns), var(--size));
  }
  div {
    background: blue;
    height: var(--size);
    width: var(--size);
  }
</style>

<main style="--size:{size}px; --columns:{columns}; --rows:{rows}">
  {#each [...grid] as column, c}
    {#each [...grid][0] as row, r}
      <div style="background: {grid[c][r] ? 'blue' : 'white'}" />
    {/each}
  {/each}
</main>
