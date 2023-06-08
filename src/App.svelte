<script>
	import { onMount } from 'svelte';
  
	let gridContainer;
	let agGrid;
  
	const columnDefs = [
	  { field: 'make' },
	  { field: 'model' },
	  { field: 'price' },
	  {
		headerName: 'Actions',
		cellRenderer: function(params) {
		  return `<button onclick="editRow(${params.rowIndex})">Edit</button>`;
		}
	  }
	];
  
	const rowData = [
	  
	{ make: 'suziki', model: 'access', price: 65000 },
	{ make: 'Hero', model: 's1', price: 95000 },
	{ make: 'Honda', model: 'Activa', price: 85000 },
	{ make: 'Toyota', model: 'Celica', price: 35000 },
	{ make: 'Ford', model: 'Mondeo', price: 32000 },
	{ make: 'Porsche', model: 'Boxter', price: 72000 }
	];
  
	function editRow(rowIndex) {
	  const selectedRowData = agGrid.gridOptions.api.getDisplayedRowAtIndex(rowIndex).data;
	  // Display the popup with the selected row data
	  window.alert(JSON.stringify(selectedRowData, null, 2));
	}
  
	onMount(() => {
	  import('ag-grid-community').then((module) => {
		agGrid = module;
		const gridOptions = {
		  columnDefs: columnDefs,
		  rowData: rowData,
		  defaultColDef: {
			sortable: true,
			unSortIcon: true,
			resizable: true,
			flex: 1,
			minWidth: 100,
		  },
		};
		new agGrid.Grid(gridContainer, gridOptions);
	  });
	});
  </script>
  
  <svelte:head>
	<script src="https://cdn.jsdelivr.net/npm/ag-grid-community@latest/dist/ag-grid-community.min.noStyle.js"></script>
	<link
	  rel="stylesheet"
	  href="https://cdn.jsdelivr.net/npm/ag-grid-community@latest/dist/styles/ag-grid.css"
	/>
	<link
	  rel="stylesheet"
	  href="https://cdn.jsdelivr.net/npm/ag-grid-community@latest/dist/styles/ag-theme-alpine.css"
	/>
  </svelte:head>
  
  <div id="datagrid" class="ag-theme-alpine" style="height: 600px; width:500px;" bind:this={gridContainer}></div>
  
  <style>
	#datagrid {
	  --ag-header-foreground-color: blue;
	}
	:global(.ag-header-cell) {
	  background: orange;
	  font-size: 16px;
	}
  </style>
  