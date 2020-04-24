<script>
  import { onMount } from "svelte";

  export let lastData;

  let container_box;
  let box_title = "Demographics";
  let chart1;
  let chart2;
  let chart3;
  let chart4;
  let container_box1;
  let container_box2;
  
  let canvasElement1;
  let canvasElement2;
  
  let ratio = 2.4;

  $: chart1 && lastData && fillChart();

  onMount(() => {
    initChart();
  });

  async function fillChart() {
    chart1.data = {
      datasets: [
        {
          data: [lastData.confirmados_m, lastData.confirmados_f],
          backgroundColor: ["#40C0A526", "#FFC83126"],
          borderColor: ["#40C0A5", "#FFC831"],
          label: ""
        }
      ],
      labels: ["Men", "Woman"]
    };


    chart2.data = {
      datasets: [
        {
          data: [lastData.obitos_m, lastData.obitos_f],
          backgroundColor: ["#40C0A526", "#FFC83126"],
          borderColor: ["#40C0A5", "#FFC831"],

          label: ""
        }
      ],
      labels: ["Men", "Woman"]
    };

    chart1.update();
    chart2.update();
  }

  function initChart() {
    ratio = (container_box1.offsetWidth-10) / (container_box1.offsetHeight-20);

    if (chart1 === undefined) {
      var ctx1 = canvasElement1.getContext("2d");
      chart1 = new Chart(ctx1, {
        type: "pie",
        data: {
          labels: [],
          datasets: []
        },
        options: {
          legend: {
            labels: {
              usePointStyle: true
            },
            display: false
          },
          tooltips: {
            mode: "index"
          },
          aspectRatio: ratio,
          responsive: true,
          hoverMode: "index",
          stacked: false,
          spanGaps: true,
          showLines: true,
          title: {
            display: false
          }
        }
      });
      var ctx2 = canvasElement2.getContext("2d");
      chart2 = new Chart(ctx2, {
        type: "pie",
        data: {
          labels: [],
          datasets: []
        },
        options: {
          legend: {
            labels: {
              usePointStyle: true
            },
            display: false
          },
          tooltips: {
            mode: "index"
          },
          aspectRatio: ratio,
          responsive: true,
          hoverMode: "index",
          stacked: false,
          spanGaps: true,
          showLines: true,
          title: {
            display: false
          }
        }
      });

    }
  }

  function changeChart(e) {
    if (chart_mode) {
      chart_mode = false;
      fillChart();
      btn_text = "Infected Count";
      box_title = "Growth Evolution";
      btn_icon = "user-friends";
    } else {
      chart_mode = true;
      fillChart();
      box_title = "Infected Count";
      btn_text = "Growth Evolution";
      btn_icon = "chart-line";
    }
  }
</script>

<style>
  .container-chart {
    width: calc(100% - 10px);
    height: calc(100vh - 210px);
    position: relative;
  }
  .chart-box{
    height: 40%;
  }
  .container-body {
    position: relative;
    height: calc(100% - 60px);
  }
</style>

<div class="container-basic container-chart" bind:this={container_box}>

  <div class="container-header">

    <div class="container-header-contents">

      <h5 class="container-title">{box_title}</h5>

    </div>
  </div>
  <div class="container-body">
      <div class="div1">Confirmed</div>
      <div class="chart-box" bind:this={container_box1}>
        <canvas id="myChart" bind:this={canvasElement1} />
      </div>
      <div class="div2">Deaths</div>
      <div class="chart-box" bind:this={container_box2} >
        <canvas id="myChart" bind:this={canvasElement2} />
    </div>
  </div>
</div>
