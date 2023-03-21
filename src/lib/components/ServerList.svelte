<script lang="ts">
    import DOMPurify from "isomorphic-dompurify";

	export let allServers: Array<any>;
        
    let tableHeaders = [
        "name",
        "players",
        "game_version",
        "has_mods",
        "has_password",
        "game_time_elapsed"
    ];

    let servers: Array<any> = [];
    let size = 100;
    let page = 0;


    
    function sanitize(rawString: any): string | any {
      const sanitizedString = !rawString
        ? ''
        : DOMPurify.sanitize(
            rawString
              .replace(
                /\[\/?(?:b|i|u|s|left|center|right|quote|code|list|img|spoil|color|item|).*?\]/g,
                ''
              )
              .trim()
          )

      return sanitizedString === '' ? rawString : sanitizedString
    };

    $: servers = [
    ...servers,
    ...allServers.slice(size * page, size * (page + 1))
  ];

</script>

{#if allServers}
    <table class="factorioTable">
        <thead>
            <tr>
                {#each tableHeaders as header}
				    <th><div class="factorioHeader">{header}</div></th>
			    {/each}
            <tr/>
        </thead>
        <tbody>
            {#each Object.values(servers) as row}
                <tr>
                    {#each tableHeaders as header}
                        {#each Object.entries(row) as [key, cell]}
                        
                            {#if header === key && !(key==='name')}
                                <td>
                                    <div class="factorioCell">{cell}</div>
                                </td>
                            {:else if header === key && key==='name'}
                                <div class="factorioCell">{sanitize(cell)}</div>
                            {/if}
                        {/each}
                    {/each}
                </tr>
            {/each}
        </tbody>
    </table>
{:else}
	<p class="loading">loading...</p>
{/if}




<style>
	.loading {
		opacity: 0;
		animation: 0.4s 0.8s forwards fade-in;
	}

    table, th, td {
		border: 1px solid;
		border-collapse: collapse;
		margin-bottom: 10px;
	}
	table.factorioTable {
		border: 1px solid #313031;
		background-color: #414040;
		width: 100%;
		text-align: left;
		border-collapse: collapse;
	}
    table.factorioTable th {
		border: 10px solid #313031;
		padding: 10px 10px;
	}
	table.factorioTable thead {
		background: #313031;
		border-bottom: 2px solid #313031;
	}
	table.factorioTable thead th {
		font-weight: bold;
		color: #8e8e8e;
		border-left: 2px solid #313031;
        
	}
    .factorioHeader {
        background-color: #8e8e8e;
        padding: 10px 12px 10px 12px;
        font-size: 100%;
        text-align: center;
        color: #000;
        font-weight: 600;
        vertical-align: baseline;
        min-width: 150px;
        display: inline-table;
        border: none;
        line-height: 36px;
        margin: auto 0;
        white-space: nowrap;
        box-shadow: inset 8px 0px 4px -8px #000, inset -8px 0px 4px -8px #000, inset 0px 10px 2px -8px #e3e3e3, inset 0px 10px 2px -8px #282828, inset 0px -9px 2px -8px #000, 0px 0px 4px 0px #000000;
        cursor: pointer;
        -webkit-user-select: none;
        -moz-user-select: none;
        user-select: none;
        height: 36px;
    }

	table.factorioTable thead th:first-child {
		border-left: none;
	}

    table.factorioTable tbody td {
        line-height: 1.25;
        padding: 10px 10px;
	}
	table.factorioTable tr:nth-child(even) {
		background: #414040;
	}
    table.factorioTable td {
		border:none;
	}
    table.factorioTable tr {
        border: 5px ridge #313031;
        margin-top: 5px;
    }
</style>