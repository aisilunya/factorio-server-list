<script lang="ts">
	import ServerList from '$lib/components/ServerList.svelte';
	import { onMount } from 'svelte';

    let url = new URL('https://api.dservindex.com/servers?has_password=false&has_mods=any&game_version=any&orderby=players&direction=desc&search=')

    let allServers: Array<any> = [];
 



    async function fetchServers(url:URL): Promise<any> {
        const response = await fetch(url)
        const data = await response.json();
        allServers = await data.servers;
    }
    
    onMount(() => fetchServers(url));
</script>


<main> 
    <ServerList {allServers}/>
</main>

<style>
    main {
        background-color: #313031;
        max-width: 1200px;
        margin: auto;
    }
</style>
