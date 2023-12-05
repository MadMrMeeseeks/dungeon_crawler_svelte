<script lang="ts">
    import {onMount} from "svelte";

    // Set up event listener for keystrokes

    onMount(() => {
        window.addEventListener('keydown', handleKeydown);
        return () => {
            window.removeEventListener('keydown', handleKeydown);
        };
    });

    // Map Layout
    let mapLayout = [
        [false, false, false, false],
        [false, false, false, false],
        [false, false, false, false],
        [false, false, false, false]
    ];

    // Player start position and tracking
    let playerPosition = { row: 3, col: 0 };

    // Initialize the starting position
    mapLayout[playerPosition.row][playerPosition.col] = true;

    function updatePlayerPosition(newRow: number, newCol: number) {
        // Update the player's position and mark the new room as visited
        if (newRow >= 0 && newRow <= 3 && newCol >= 0 && newCol <= 3) {
            mapLayout[newRow][newCol] = true;
            playerPosition = { row: newRow, col: newCol };
        }
    }

    // Handle key events for movement
    function handleKeydown(event: KeyboardEvent) {
        if (event.key === 'ArrowUp') {
            updatePlayerPosition(playerPosition.row - 1, playerPosition.col);
        } else if (event.key === 'ArrowDown') {
            updatePlayerPosition(playerPosition.row + 1, playerPosition.col);
        } else if (event.key === 'ArrowLeft') {
            updatePlayerPosition(playerPosition.row, playerPosition.col - 1);
        } else if (event.key === 'ArrowRight') {
            updatePlayerPosition(playerPosition.row, playerPosition.col + 1);
        }
    }



</script>

<main>
    <table>
        <tbody>
        {#each mapLayout as row, rowIndex}
            <tr>
                {#each row as cell, cellIndex}
                    <td class={cell ? "defaultroomstyle" : ""} on:click={() => updatePlayerPosition(rowIndex, cellIndex)}>
                        {#if playerPosition.row === rowIndex && playerPosition.col === cellIndex}
                            <span class="reddot"></span>
                        {/if}
                    </td>
                {/each}
            </tr>
        {/each}
        </tbody>
    </table>
</main>


<style>
    .defaultroomstyle {
        border: medium solid white;
        background-color: #535bf2;
    }

    td {
        width: 75px;
        height: 75px;
        border: medium solid white;
        position: relative; /* For positioning the red dot */
    }

    .reddot {
        height: 10px;
        width: 10px;
        background-color: red;
        border-radius: 50%;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        animation: flash 1s infinite;
    }

    @keyframes flash {
        0%, 100% { opacity: 1; }
        50% { opacity: 0; }
    }
</style>

