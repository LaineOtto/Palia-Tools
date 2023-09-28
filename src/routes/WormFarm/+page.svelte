<script>
	import { readable } from 'svelte/store';
	import { createTable, Subscribe, Render } from 'svelte-headless-table';
	import { addSortBy, addColumnOrder, addHiddenColumns } from 'svelte-headless-table/plugins';
	import tableData from '$lib/data/wormFarm.js';

	const data = readable(tableData);

	const table = createTable(data, {
		sort: addSortBy({
			toggleOrder: ['asc', 'desc'],
			initialSortKeys: [{ id: 'goldPerMinute', order: 'asc' }]
		}),
		colOrder: addColumnOrder(),
		hideCols: addHiddenColumns({
			initialHiddenColumnIds: []
		})
	});

	const columns = table.createColumns([
		table.column({
			header: 'Crop',
			accessor: 'crop'
		}),
		table.column({
			header: 'Crop Sale Value',
			accessor: 'cropSaleValue'
		}),
		table.column({
			header: 'Farm Sale Value',
			accessor: 'farmSaleValue'
		}),
		table.column({
			header: 'Net Value',
			accessor: 'netValue'
		}),
		table.column({
			header: 'Gold per Minute',
			accessor: 'goldPerMinute',
			plugins: {
				sort: { invert: true }
			}
		})
	]);

	const { flatColumns, headerRows, rows, tableAttrs, tableBodyAttrs, pluginStates } =
		table.createViewModel(columns);
	const { hiddenColumnIds } = pluginStates.hideCols;

	// creates a map of all column ids set to false(visible)
	const ids = flatColumns.map((c) => c.id);
	let hideForId = Object.fromEntries(ids.map((id) => [id, false]));

	// sets the columns defined in initial state as true(hidden)
	let hideId = $hiddenColumnIds.map((id) => [id, true]);
	for (const id of hideId) {
		// @ts-ignore
		// this gives an error but works anyways so who knows
		hideForId[id[0]] = true;
	}

	// Filters hideForId to only entries with a hide value and assigns the ids
	// of those entries to hiddenColumnIds.
	$: $hiddenColumnIds = Object.entries(hideForId)
		.filter(([, hide]) => hide)
		.map(([id]) => id);
</script>

<svelte:head>
	<title>Worm Farm</title>
	<meta name="description" content="Worm Farm Gold Comparison" />
</svelte:head>

<table {...$tableAttrs}>
	<thead>
		{#each $headerRows as headerRow (headerRow.id)}
			<Subscribe rowAttrs={headerRow.attrs()} let:rowAttrs>
				<tr {...rowAttrs}>
					{#each headerRow.cells as cell (cell.id)}
						<Subscribe attrs={cell.attrs()} let:attrs props={cell.props()} let:props>
							<th {...attrs} on:click={props.sort.toggle}>
								{#if props.sort.order === 'asc'}
									⬇️
								{:else if props.sort.order === 'desc'}
									⬆️
								{/if}
								<Render of={cell.render()} />
							</th>
						</Subscribe>
					{/each}
				</tr>
			</Subscribe>
		{/each}
	</thead>
	<tbody {...$tableBodyAttrs}>
		{#each $rows as row (row.id)}
			<Subscribe rowAttrs={row.attrs()} let:rowAttrs>
				<tr {...rowAttrs}>
					{#each row.cells as cell (cell.id)}
						<Subscribe attrs={cell.attrs()} let:attrs>
							<td {...attrs}>
								<Render of={cell.render()} />
							</td>
						</Subscribe>
					{/each}
				</tr>
			</Subscribe>
		{/each}
	</tbody>
</table>

{#each ids as id}
	<div>
		<input id="hide-{id}" type="checkbox" bind:checked={hideForId[id]} />
		<label for="hide-{id}">{id}</label>
	</div>
{/each}
