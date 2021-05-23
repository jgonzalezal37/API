<script>
	import Router from 'svelte-spa-router';
	import Landing from './Landing.svelte';
	import OilTable from './oilAPI/OilTable.svelte';
	import OilEdit from './oilAPI/OilTableEdit.svelte';
	import OilGraph from './oilAPI/OilGraph.svelte';
	
	import NotFound from './NotFound.svelte';
	import Info from './Info.svelte';
	
	
	


	const routes = {
		"/": Landing,
		"/oil-production-stats": OilTable,
		"/oil-production-stats/:country/:year": OilEdit,
		"/info": Info,
		"/oil-graph": OilGraph,
		"*": NotFound
	};
</script>

<main>
	<Router {routes} />
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>