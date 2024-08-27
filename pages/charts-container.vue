<template>
  <div>
    <NuxtLink to="/">range chart</NuxtLink>
    <NuxtLink to="/pivot">pivot</NuxtLink>
    <NuxtLink to="/pivot-active">pivot active</NuxtLink>
    <NuxtLink to="/pivot-totals">pivot totals</NuxtLink>
    <NuxtLink to="/charts">charts</NuxtLink>
  </div>

  <h2>Charts Container</h2>

  <div id="container">
    <ag-grid-vue style="width: 98%; height: 100%;" :class="themeClass" :columnDefs="columnDefs"
      @grid-ready="onGridReady" :defaultColDef="defaultColDef" :autoGroupColumnDef="autoGroupColumnDef"
      :rowData="rowData" :enableCharts="true" :enableRangeSelection="true" :enableRangeHandle="true"
      rowGroupPanelShow="always" :pivotMode="true" :sideBar="true" :createChartContainer="createChartContainer">
    </ag-grid-vue>
  </div>

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
      { field: "country", enableRowGroup: true, enablePivot: true },
      { field: "year", chartType: 'category', enableRowGroup: true, enablePivot: true },
      { field: "date", enableRowGroup: true, enablePivot: true }, // disappear with pivot 
      { field: "athlete" }, // disappear with pivot 
      { field: "sport", enableRowGroup: true, enablePivot: true },
      { field: "gold", chartType: 'series', aggFunc: "sum" },
      { field: "silver", chartType: 'series', aggFunc: "sum" },
      { field: "bronze", chartType: 'series', aggFunc: "sum" },
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

    const createChartContainer = (chartRef) => {
      console.log(chartRef);

      const eChart = chartRef.chartElement;
      const eParent = document.querySelector("#container");
      const chartWrapperHTML = `
        <div class="chart-wrapper ag-theme-quartz-dark}">
          <div class="chart-wrapper-top">
            <h2 class="chart-wrapper-title">Chart Container</h2>
            <button class="chart-wrapper-close">Destroy Chart</button>
          </div>
          <div class="chart-wrapper-body"></div>
        </div>
      `;

      eParent.insertAdjacentHTML("beforeend", chartWrapperHTML); // inserts the resulting nodes inside the element after its last child

      const eChartWrapper = eParent.lastElementChild;

      eChartWrapper.querySelector(".chart-wrapper-body").appendChild(eChart); // append chart to an element
      eChartWrapper
        .querySelector(".chart-wrapper-close")
        .addEventListener("click", () => {
          chartRef.destroyChart();
          eParent.removeChild(eChartWrapper);
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
      createChartContainer
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