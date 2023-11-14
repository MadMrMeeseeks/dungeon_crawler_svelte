<script lang="ts">

    let mapLayout = [
        [true, true, false, false],
        [true, true, true, false],
        [false, false, true, true],
        [true, true, true, false]
    ];


</script>

<main>
    <table>
        <tbody>
        {#each mapLayout as row}
            <tr>
                {#each row as cell}
                    <td class={cell ? "defaultroomstyle":""}></td>
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
    }


</style>
