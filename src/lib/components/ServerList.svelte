<script lang="ts">
    import DOMPurify from "isomorphic-dompurify";
    import InfiniteLoading from 'svelte-infinite-loading';
        
    let tableHeaders = [
        {"name": "Name"},
        {"players": "Players"},
        {"game_version": "Game version"},
        {"has_mods": "Mods"},
        {"has_password": "Private"},
        {"game_time_elapsed": "Game time elapsed"}
    ];

    let url = new URL('https://api.dservindex.com/servers?has_password=false&has_mods=any&game_version=any&orderby=players&direction=desc&search=')


    let servers: Array<any> = [];
    let allServers: Array<any> = [];
    let size = 30;
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

    async function fetchServers(url:URL): Promise<any> {
        const response = await fetch(url)
        const data = await response.json();
        allServers = await data.servers;
    }

    $: servers = [
        ...servers,
        ...allServers.slice(size * page, size * (page + 1))
    ];

  async function infiniteHandler({ detail: { loaded, complete }}: any): Promise<void> {
    await fetchServers(url);
    if (servers.length < allServers.length) {
        page = page + 1;
        servers = [
        ...servers,
        ...allServers.slice(size * page, size * (page + 1))
        ];
        loaded();
    } else {
        complete();
    }
	}
</script>


<div>
    {#if allServers}
        <table class="factorioTable">
            <thead>
                <tr>
                    {#each tableHeaders as header}
                        <th>
                            <div class="factorioHeader">
                                {Object.values(header)[0]}
                            </div>
                        </th>
                    {/each}
                <tr/>
            </thead>
            <tbody>
                {#each Object.values(servers) as row}
                    <tr>
                        {#each tableHeaders as header}
                            {#each Object.entries(row) as [key, cell]}
                                {#if Object.keys(header)[0] === key && !(key==='name')}
                                    <td>
                                        <div class="factorioCell">{cell}</div>
                                    </td>
                                {:else if Object.keys(header)[0] === key && key==='name'}
                                    <div class="factorioCell">
                                        {sanitize(cell)}
                                    </div>
                                {/if}
                            {/each}
                        {/each}
                    </tr>
                {/each}
                <InfiniteLoading on:infinite={infiniteHandler} />
            </tbody>
            
        </table>
    {:else}
        <p class="loading">loading...</p>
    {/if}
</div>


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
        min-width: 200px;
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
    .factorioCell {
        padding-left: 5px;
        height: 100%;
    }
</style>