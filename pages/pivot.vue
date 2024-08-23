<template>
  <div>
    <NuxtLink to="/"><- see range chart</NuxtLink>
    <h1>Vue Grid graph demo</h1>
  </div>

  <h2>Pivot Chart</h2>
  <ag-grid-vue style="width: 98%; height: 100%;" :class="themeClass" :columnDefs="columnDefs" @grid-ready="onGridReady"
    :defaultColDef="defaultColDef" :autoGroupColumnDef="autoGroupColumnDef" :rowData="rowData" :enableCharts="true"
    :enableRangeSelection="true" :enableRangeHandle="true" rowGroupPanelShow="always" sideBar="columns">
  </ag-grid-vue>
</template>

<script>
import { ref, shallowRef, onBeforeMount } from "vue";
import "ag-grid-enterprise"; // community + advanced functionalities
import "ag-grid-charts-enterprise"; // community + advanced functionalities
import "ag-grid-community/styles/ag-grid.css"; // Mandatory CSS required by the Data Grid
import "ag-grid-community/styles/ag-theme-quartz.css"; // Optional Theme applied to the Data Grid
import { AgGridVue } from "ag-grid-vue3"; // Vue Data Grid Component

export default {
  components: {
    AgGridVue,
  },
  setup(props) {
    const columnDefs = ref([
      { field: "country", rowGroup: true, enablePivot: true },
      { field: "year", rowGroup: true, enablePivot: true },
      { field: "athlete" },
      { field: "sport", rowGroup: true },
      { field: "gold" },
      { field: "silver" },
      { field: "bronze" },
    ]);
    const gridApi = shallowRef();
    const defaultColDef = ref({
      flex: 1,
      minWidth: 100,
    });
    const autoGroupColumnDef = ref(null);
    const rowData = ref(null);

    onBeforeMount(() => {
      autoGroupColumnDef.value = {
        minWidth: 200,
      };
    });

    const onGridReady = (params) => {
      gridApi.value = params.api;

      const updateData = (data) => {
        (rowData.value = data)
        console.log(rowData.value);
      };

      fetch("https://www.ag-grid.com/example-assets/olympic-winners.json")
        .then((resp) => resp.json())
        .then((data) => {
          updateData(data);
        })
        .catch((error) => {
          console.error('Error fetching data:', error);
        });
    };

    return {
      columnDefs,
      gridApi,
      defaultColDef,
      autoGroupColumnDef,
      rowData,
      onGridReady,
      themeClass:
        "ag-theme-quartz-dark",
    };
  },
};
</script>

<style>
.ag-root-wrapper-body.ag-layout-normal.ag-focus-managed {
  height: 10%;
}
</style>