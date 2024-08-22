<template>
  <h1>Vue Grid graph demo</h1>

  <h2>Pivot Chart</h2>
  <ag-grid-vue
    style="width: 98%; height: 500px"
    class="ag-theme-quartz"
    @grid-ready="onGridReady"
    :gridOptions="gridOptions"
    rowGroupPanelShow="always"
  >
  </ag-grid-vue>
</template>

<script>
import { ref } from "vue";
import "ag-grid-enterprise"; // community + advanced functionalities
import "ag-grid-charts-enterprise"; // community + advanced functionalities
import "ag-grid-community/styles/ag-grid.css"; // Mandatory CSS required by the Data Grid
import "ag-grid-community/styles/ag-theme-quartz.css"; // Optional Theme applied to the Data Grid
import { AgGridVue } from "ag-grid-vue3"; // Vue Data Grid Component

export default {
  components: {
    AgGridVue,
  },
  setup() {
    const rowDataPivot = ref([
      {
        athlete: "Michael Phelps",
        age: 23,
        country: "USA",
        year: 2008,
        date: "24/08/2008",
        sport: "Swimming",
        gold: 8,
        silver: 0,
        bronze: 0,
        total: 8,
      },
      {
        athlete: "Usain Bolt",
        age: 22,
        country: "Jamaica",
        year: 2008,
        date: "24/08/2008",
        sport: "Athletics",
        gold: 3,
        silver: 0,
        bronze: 0,
        total: 3,
      },
      {
        athlete: "Usain Bolt",
        age: 22,
        country: "Jamaica",
        year: 2008,
        date: "24/08/2008",
        sport: "Athletics",
        gold: 3,
        silver: 0,
        bronze: 0,
        total: 3,
      },
      {
        athlete: "Michael Phelps",
        age: 23,
        country: "USA",
        year: 2008,
        date: "24/08/2008",
        sport: "Swimming",
        gold: 8,
        silver: 0,
        bronze: 0,
        total: 8,
      },
    ]);

    const colDefsPivot = ref([
      { field: "country", rowGroup: true, enableRowGroup: true },
      { field: "sport", pivot: true },
      { field: "year" },
      { field: "date" },
      { field: "gold", aggFunc: "sum" },
      { field: "silver", aggFunc: "sum" },
      { field: "bronze", aggFunc: "sum" },
    ]);

    const gridOptions = ref({
      columnDefs: colDefsPivot.value,
      rowData: rowDataPivot.value,
      enableCharts: true,
      enableRangeSelection: true,
      pivotMode: true, // Habilita o pivot mode
      chartThemes: ["ag-pastel", "ag-material-dark"],
      contextMenuItems: (params) => [
        "copy",
        "paste",
        "pivotChart", // Adiciona a opção de Pivot Chart ao menu de contexto
        "chartRange", // Adiciona a opção de Chart Range ao menu de contexto
        "export",
      ],
    });

    const gridApi = shallowRef();
    const onGridReady = (params) => {
      gridApi.value = params.api;

      const updateData = (data) => (rowDataPivot.value = data);

      fetch("https://www.ag-grid.com/example-assets/olympic-winners.json")
        .then((resp) => {
          resp.json()
          console.log(resp.json());
          
        })
        .then((data) => {
          updateData(data)
          console.log(data);
          
        });
    };

    return {
      gridOptions,
      onGridReady,
    };
  },
};
</script>
