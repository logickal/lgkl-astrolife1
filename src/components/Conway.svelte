<script lang="ts">
    import { onMount } from "svelte";

    let grid: boolean[][] = [];
    const numRows = 10;
    const numCols = 10;
    const cellSize = 5;

    function initializeGrid() {
        grid = Array.from({ length: numRows }, () => Array.from({ length: numCols }, () => Math.random() > 0.7));
    }

    function updateGrid() {
        const newGrid = grid.map((row, i) => row.map((cell, j) => {
            const neighbors = countNeighbors(i, j);
            if (cell && neighbors < 2 || neighbors > 3) {
                return false; // cell dies
            } else if (!cell && neighbors === 3) {
                return true; // Cell is born
            } else {
                return cell; // cell survives
            }
        })
        );
        grid = newGrid;
    }


    function countNeighbors(x: number, y: number): number {
        let count = 0;
        for (let i = -1; i <=1; i++) {
            for (let j=-1; j<=1; j++) {
                if (i === 0 && j === 0) continue;
                const row = (x + i + numRows) % numRows;
                const col = (y + j + numCols) % numCols;
                count += grid[row][col] ? 1 : 0;
            }
        }
        return count;
    }

    onMount(() => {
        console.log('mounted');
        initializeGrid();
        const interval = setInterval(() => {
            updateGrid();
        }, 200);
        return () => clearInterval(interval);
    });

</script>

<div class="mx-auto w-auto grid grid-cols-{numCols} col-auto row-auto gap-1 bg-gray-700">
    {#each grid as row, i}
     {#each row as cell, j}
        <div class="w-20 h-20 bg-{cell ? 'gray-600' : 'white-100'} border-1 border-black " />
     {/each}
    {/each}
</div>