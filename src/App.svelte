<script lang="ts">
    import {onMount} from "svelte";
    import Icon from '@iconify/svelte';

    // Set up event listener for keystrokes

    onMount(() => {
        window.addEventListener('keydown', handleKeydown);
        return () => {
            window.removeEventListener('keydown', handleKeydown);
        };
    });


    // Track collected items
    let hasGun = false;
    let hasHelmet = false;

    let needReset = false;

    // Model Tracking
    let showModal = false;
    let modalMessage = "";

    // Map Layout
    let grid = 8;
    let mapLayout = Array(grid).fill(false).map(() => Array(grid).fill(false));

    // Randomly place items and enemy
    let gunPosition = { row: Math.floor(Math.random() * grid), col: Math.floor(Math.random() * grid) };
    let helmetPosition = { row: Math.floor(Math.random() * grid), col: Math.floor(Math.random() * grid) };
    let enemyPosition = { row: Math.floor(Math.random() * grid), col: Math.floor(Math.random() * grid) };

    // Ensure gun, helmet, and enemy are not in the same room
    while (JSON.stringify(gunPosition) === JSON.stringify(helmetPosition) || JSON.stringify(gunPosition) === JSON.stringify(enemyPosition) || JSON.stringify(helmetPosition) === JSON.stringify(enemyPosition)) {
        helmetPosition = { row: Math.floor(Math.random() * grid), col: Math.floor(Math.random() * grid) };
        enemyPosition = { row: Math.floor(Math.random() * grid), col: Math.floor(Math.random() * grid) };
    }

    // Player start position and tracking
    let playerPosition = { row: 0, col: 0 };

    // Initialize the starting position
    mapLayout[playerPosition.row][playerPosition.col] = true;

    function updatePlayerPosition(newRow: number, newCol: number) {
        if (newRow >= 0 && newRow < grid && newCol >= 0 && newCol < grid) {
            playerPosition = { row: newRow, col: newCol };
            mapLayout[newRow][newCol] = true; // Mark as visited

            // Check for item collection
            if (playerPosition.row === gunPosition.row && playerPosition.col === gunPosition.col && !hasGun) {
                hasGun = true;
                showModal = true;
                modalMessage = "You got the gun!";
                needReset = false;
            }
            if (playerPosition.row === helmetPosition.row && playerPosition.col === helmetPosition.col && !hasHelmet) {
                hasHelmet = true;
                showModal = true;
                modalMessage = "You got the helmet!";
                needReset = false;
            }

            // Check for enemy encounter
            if (playerPosition.row === enemyPosition.row && playerPosition.col === enemyPosition.col) {
                showModal = true;
                needReset = true;
                if (!hasGun && !hasHelmet) {
                    modalMessage = "You were shot in the face and died. Click reset to play again.";
                } else if (hasGun && !hasHelmet) {
                    modalMessage = "You were shot in the face and died. Click reset to play again.";
                } else if (!hasGun && hasHelmet) {
                    modalMessage = "Your helmet deflected the shot to the face, but you have no weapon so the monster is now eating you alive. Click reset to play again.";
                } else if (hasGun && hasHelmet)
                    modalMessage = "Your helmet deflects the shot to the face and you use your gun to blast the monster off of a cliff.";
                // Add logic if player has both gun and helmet
            }
        }
    }

    // Handle key events for movement
    function handleKeydown(event: KeyboardEvent) {
        // If the modal is shown, ignore key presses
        if (showModal) {
            return;
        }

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

    function resetGame() {
        playerPosition = { row: 0, col: 0 };
        hasGun = false;
        hasHelmet = false;
        showModal = false;

        // Randomize positions
        gunPosition = { row: Math.floor(Math.random() * grid), col: Math.floor(Math.random() * grid) };
        helmetPosition = { row: Math.floor(Math.random() * grid), col: Math.floor(Math.random() * grid) };
        enemyPosition = { row: Math.floor(Math.random() * grid), col: Math.floor(Math.random() * grid) };

        // Reset map layout
        mapLayout = Array(grid).fill(false).map(() => Array(grid).fill(false));
        mapLayout[playerPosition.row][playerPosition.col] = true;
    }



</script>

<main>
    <div class="header">
        <h2>Items:</h2>
        <div class="items">
            {#if hasGun}
                <Icon icon="game-icons:ray-gun" width="50" height="50" />
            {/if}
            {#if hasHelmet}
                <Icon icon="game-icons:dwarf-helmet" width="50" height="50" />
            {/if}
        </div>
    </div>
    <table>
        <tbody>
        {#each mapLayout as row, rowIndex}
            <tr>
                {#each row as cell, cellIndex}
                    <td class={cell ? "defaultroomstyle" : "" + (enemyPosition.row === rowIndex && enemyPosition.col === cellIndex ? " enemy" : "")} on:click={() => updatePlayerPosition(rowIndex, cellIndex)}>

                    {#if playerPosition.row === rowIndex && playerPosition.col === cellIndex}
                            <span class="reddot"></span>
                        {/if}
                        <!-- Icons are hidden initially and shown only when conditions are met -->
                    </td>
                {/each}
            </tr>
        {/each}
        </tbody>
    </table>
    {#if showModal}
        <div class="modal">
            <p>{modalMessage}</p>
            {#if needReset}
                <button on:click={resetGame}>Reset</button>
            {:else}
                <button on:click={() => showModal = false}>OK</button>
            {/if}
        </div>
    {/if}
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

    body {
        background-color: grey;
    }

    @keyframes flash {
        0%, 100% { opacity: 1; }
        50% { opacity: 0; }
    }

    .header {
        text-align: left; /* Align the text to the left */
        margin-bottom: 10px; /* Space between the header and the table */
    }

    .items {
        display: flex; /* Flexbox to align items horizontally */
        align-items: center; /* Center items vertically */
        gap: 10px; /* Space between items */
    }

    h2 {
        margin: 0; /* Remove default margin */
        padding: 0; /* Remove default padding */
    }

    .enemy {
        background-color: rgba(108, 49, 49, 0.86);
    }

    .modal {
        position: fixed;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background-color: white;
        padding: 20px;
        border: 1px solid #ccc;
        z-index: 1000;
        color: black; /* Or any color that contrasts well with the background */
        background-color: white; /* Ensure good contrast with the text color */
    }
</style>

