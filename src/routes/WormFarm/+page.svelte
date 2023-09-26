<script>
    import { readable } from 'svelte/store';
    import { createTable, Subscribe, Render, } from 'svelte-headless-table';
    import { addSortBy, addColumnOrder } from 'svelte-headless-table/plugins';
    import tableData from '$lib/data/wormFarm.js';

    // console.log(tableData);
  
    const data = readable(tableData);
  
    const table = createTable(data, {
        sort: addSortBy({}),
        colOrder: addColumnOrder(),
    });
  
    const columns = table.createColumns([
      table.column({
        header: 'Crop',
        accessor: 'crop',
      }),
      table.column({
        header: 'Crop Yield',
        accessor: 'cropYield',
      }),
      table.column({
        header: 'Farm Yield',
        accessor: 'farmYield',
      }),
      table.column({
        header: 'Gain',
        accessor: 'gain',
      }),
      table.column({
        header: 'Gold/Minute',
        accessor: 'goldPerMinute',
        plugins: {
            sort: { invert: true },
        },
      }),
    ]);
  
    const { headerRows, rows, tableAttrs, tableBodyAttrs } = table.createViewModel(columns);
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
              <Subscribe
                attrs={cell.attrs()} let:attrs
                props={cell.props()} let:props
              >
                <th {...attrs} on:click={props.sort.toggle}>
                  <Render of={cell.render()} />
                  {#if props.sort.order === 'asc'}
                    ⬇️
                  {:else if props.sort.order === 'desc'}
                    ⬆️
                  {/if}
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

  <style>
      table {
          border-spacing: 0;
          border-top: 1px solid black;
          border-left: 1px solid black;
      }
      th, td {
          border-bottom: 1px solid black;
          border-right: 1px solid black;
          padding: 0.5rem;
      }
  </style>