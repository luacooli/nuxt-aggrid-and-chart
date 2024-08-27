<template>
  <div>
    <NuxtLink to="/">range chart</NuxtLink>
    <NuxtLink to="/pivot-active">pivot active</NuxtLink>
    <NuxtLink to="/pivot-totals">pivot totals</NuxtLink>
    <NuxtLink to="/charts">charts</NuxtLink>
    <NuxtLink to="/charts-container">charts container</NuxtLink>
  </div>

  <h2>Pivot Mode</h2>
  <ag-grid-vue style="width: 98%; height: 100%;" :class="themeClass" :columnDefs="columnDefs" @grid-ready="onGridReady"
    :defaultColDef="defaultColDef" :autoGroupColumnDef="autoGroupColumnDef" :rowData="rowData" :enableCharts="true"
    :enableRangeSelection="true" :enableRangeHandle="true" rowGroupPanelShow="always" :pivotMode="true" :sideBar="true"
    :enablePivot=true>
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
      { field: "country", rowGroup: true, enableRowGroup: true },
      { field: "year", rowGroup: true, enableRowGroup: true, enablePivot: true },
      { field: "date", enablePivot: true }, // disappear with pivot 
      { field: "athlete" }, // disappear with pivot 
      { field: "sport", pivot: true },
      { field: "gold", aggFunc: "sum" },
      { field: "silver", aggFunc: "sum" },
      { field: "bronze", aggFunc: "sum" },
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
  height: 700px;
}
</style>