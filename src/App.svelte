<script>
  import { onMount } from 'svelte';

  let gridContainer;
  let agGrid;
  let gridApi;

  const columnDefs = [
    { headerName: 'First Name', field: 'firstName' },
    { headerName: 'Surname', field: 'surname' },
    { headerName: 'Email', field: 'email' },
    { headerName: 'Mobile', field: 'mobile' },
    { headerName: 'Reference', field: 'reference' }
  ];

  let rowData = [];
  let searchQuery = '';

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
          floatingFilter: true
        },
        enableBrowserTooltips: true,
        pagination: true,
        paginationPageSize:10,
        suppressRowClickSelection: true,
        rowSelection: 'multiple',
        floatingFilter: true,
        onGridReady: function (params) {
          gridApi = params.api;
          gridApi.sizeColumnsToFit();
        }
      };
      new agGrid.Grid(gridContainer, gridOptions);

      // Fetch data from the API
      fetch('https://api.recruitly.io/api/candidate/?apiKey=TEST1236C4CF23E6921C41429A6E1D546AC9535E')
        .then((response) => response.json())
        .then((data) => {
          // Check if the data contains the expected properties
          if (Array.isArray(data.data)) {
            // Extract the desired fields from the response data
            rowData = data.data.map((candidate) => ({
              firstName: candidate.firstName,
              surname: candidate.surname,
              email: candidate.email,
              mobile: candidate.mobile,
              reference: candidate.reference
            }));
            gridOptions.api.setRowData(rowData);
          } else {
            console.error('Invalid API response:', data);
          }
        })
        .catch((error) => {
          console.error('Error fetching data:', error);
        });
    });
  });

  function search() {
    gridApi.setQuickFilter(searchQuery);
  }
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

  <!-- Add Bootstrap CSS -->
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css"
  />
</svelte:head>

<div class="container d-flex justify-content-center align-items-center vh-100">
  <div class="d-flex flex-column">
    <div class="mb-3">
      <input type="text" class="form-control" placeholder="Search" bind:value={searchQuery} on:input={search} />
    </div>
    <div id="datagrid" class="ag-theme-alpine" style="height: 600px; width: 800px;" bind:this={gridContainer}></div>
  </div>
</div>

<style>
  #datagrid {
    --ag-header-foreground-color: blue;
  }
  :global(.ag-header-cell) {
    font-size: 16px;
  }
  :global(.ag-header-group-cell) {
    border-right: 1px solid lightgray;
  }
</style>
