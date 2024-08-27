<template>
  <div>
    <NuxtLink to="/">range chart</NuxtLink>
    <NuxtLink to="/pivot">pivot</NuxtLink>
    <NuxtLink to="/pivot-active">pivot active</NuxtLink>
  </div>

  <h2>Pivot Totals</h2>

  <ag-grid-vue style="width: 98%; height: 100%;" :class="themeClass" :columnDefs="columnDefs" @grid-ready="onGridReady"
    :defaultColDef="defaultColDef" :autoGroupColumnDef="autoGroupColumnDef" :rowData="rowData" :enableCharts="true"
    :enableRangeSelection="true" :enableRangeHandle="false" :pivotMode="true" :sideBar="true"
    pivotColumnGroupTotals="before" :processPivotResultColGroupDef="processPivotResultColGroupDef">
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
      { field: "country", rowGroup: true, enableRowGroup: true, enablePivot: true },
      { field: "sport", enableRowGroup: true, enablePivot: true },
      { field: "year", enableRowGroup: true, pivot: true, enablePivot: true },
      { field: "date", enableRowGroup: true, enablePivot: true }, // disappear with pivot 
      { field: "athlete" }, // disappear with pivot 
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
    const processPivotResultColGroupDef = (colGroupDef) => {
      if (colGroupDef.pivotKeys.length && colGroupDef.pivotKeys[0] === '2000') {
        colGroupDef.headerClass = 'color-background'
      }
      colGroupDef.headerName = ':) ' + colGroupDef.headerName
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
      processPivotResultColGroupDef
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

.color-background {
  background: red;
}

.ag-root-wrapper-body.ag-layout-normal.ag-focus-managed {
  height: 700px;
}
</style>