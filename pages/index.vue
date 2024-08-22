<template>
  <h1>Vue Grid graph demo</h1>

  <h2>Range Chart</h2>
  <ag-grid-vue
    :rowData="rowData"
    :columnDefs="columnDefs"
    style="width: 98%; height: 500px"
    class="ag-theme-quartz"
    :enableCharts="true"
    :enableRangeSelection="true"
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
    const columnDefs = ref([
      { field: "athlete", headerName: "Athlete", minWidth: 150 },
      { field: "gold", headerName: "Gold Medals" },
      { field: "silver", headerName: "Silver Medals" },
      { field: "bronze", headerName: "Bronze Medals" },
    ]);

    const rowData = ref([
      { athlete: "Michael Phelps", gold: 8, silver: 0, bronze: 0 },
      { athlete: "Usain Bolt", gold: 3, silver: 0, bronze: 0 },
      { athlete: "Simone Biles", gold: 4, silver: 1, bronze: 1 },
      { athlete: "Katie Ledecky", gold: 4, silver: 0, bronze: 0 },
    ]);

    return {
      rowData,
      columnDefs,
    };
  },
};
</script>
