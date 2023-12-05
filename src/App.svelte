<script lang="ts">


    let mapLayout = [
        [false, false, false, false],
        [false, false, false, false],
        [false, false, false, false],
        [false, false, false, false]
    ];

    function flipRoomIfExists(row: number, col: number) {
        if (row < 0 || row > 3 || col < 0 || col > 3) {
            return;
        }
        else {
            mapLayout[row][col] = !mapLayout[row][col];
        }
    }

    function roomClick(row: number, col: number) {

        flipRoomIfExists(row, col);
        flipRoomIfExists(row + 1, col);
        flipRoomIfExists(row - 1, col);
        flipRoomIfExists(row, col + 1);
        flipRoomIfExists(row, col - 1);

    }

</script>

<main>
    <table>
        <tbody>
        {#each mapLayout as row, rowIndex}
            <tr>
                {#each row as cell, cellIndex}
                    <td class={cell ? "defaultroomstyle":""} on:click={() => roomClick(rowIndex, cellIndex)}></td>
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
    }


</style>
