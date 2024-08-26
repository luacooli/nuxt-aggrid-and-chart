<template>
  <div>
    <NuxtLink to="/">see range chart</NuxtLink>
    <NuxtLink to="/pivot">see pivot</NuxtLink>
  </div>

  <h2>Pivot Mode and pivot active</h2>
  <div class="btn__container">
    <button @click="onBtNormal()">1 - Grouping Active</button>
    <button @click="onBtPivotMode()">2 - Grouping Active with Pivot Mode</button>
    <button @click="onBtFullPivot()">3 - Grouping Active with Pivot Mode and Pivot Active</button>
    <button @click="onBtFullTwoPivot()">4 - Grouping Active with Pivot Mode and Two Pivots Active</button>
  </div>

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
      { field: "year", rowGroup: true, enableRowGroup: true },
      { field: "date", enableRowGroup: true }, // disappear with pivot 
      { field: "athlete" }, // disappear with pivot 
      { field: "sport", pivot: true, enableRowGroup: true },
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

    const onBtNormal = () => {
      gridApi.value.setGridOption("pivotMode", false);
      gridApi.value.applyColumnState({
        state: [
          { colId: "country", rowGroup: true },
          { colId: "year", rowGroup: true },
        ],
        defaultState: {
          pivot: false,
          rowGroup: false,
        },
      });
    };
    const onBtPivotMode = () => {
      gridApi.value.setGridOption("pivotMode", true);
      gridApi.value.applyColumnState({
        state: [
          { colId: "country", rowGroup: true },
          { colId: "year", rowGroup: true },
        ],
        defaultState: {
          pivot: false,
          rowGroup: false,
        },
      });
    };
    const onBtFullPivot = () => {
      gridApi.value.setGridOption("pivotMode", true);
      gridApi.value.applyColumnState({
        state: [
          { colId: "country", rowGroup: true },
          { colId: "year", pivot: true },
        ],
        defaultState: {
          pivot: false,
          rowGroup: false,
        },
      });
    };
    const onBtFullTwoPivot = () => {
      gridApi.value.setGridOption("pivotMode", true);
      gridApi.value.applyColumnState({
        state: [
          { colId: "country", pivot: true },
          { colId: "year", pivot: true },
        ],
        defaultState: {
          pivot: false,
          rowGroup: false,
        },
      });
    };

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
      onBtNormal,
      onBtPivotMode,
      onBtFullPivot,
      onBtFullTwoPivot,
    };
  },
};
</script>

<style>
a {
  margin-right: 1rem;
}

.btn__container button {
  margin: 0.6rem;
}

.ag-root-wrapper-body.ag-layout-normal.ag-focus-managed {
  height: 700px;
}
</style>