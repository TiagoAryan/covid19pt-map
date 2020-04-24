<script>
  import { onMount } from "svelte";

  export let lastData;

  let container_box;
  let box_title = "Male Female Diff";
  let chart1;
  let chart2;
  let chart3;
  let chart4;
  let container_box1;
  let container_box2;
  let container_box3;
  let container_box4;
  let canvasElement1;
  let canvasElement2;
  let canvasElement3;
  let canvasElement4;
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

    let age_c = [
      "0-9",
      "10-19",
      "20-29",
      "30-39",
      "40-49",
      "50-59",
      "60-69",
      "70-79",
      "+80"
    ];
    let age_c_m = [];
    let age_c_f = [];

    age_c_m.push(lastData.confirmados_0_9_m);
    age_c_m.push(lastData.confirmados_10_19_m);
    age_c_m.push(lastData.confirmados_20_29_m);
    age_c_m.push(lastData.confirmados_30_39_m);
    age_c_m.push(lastData.confirmados_40_49_m);
    age_c_m.push(lastData.confirmados_50_59_m);
    age_c_m.push(lastData.confirmados_60_69_m);
    age_c_m.push(lastData.confirmados_70_79_m);
    age_c_m.push(lastData.confirmados_80_plus_m);

    age_c_f.push(lastData.confirmados_0_9_f);
    age_c_f.push(lastData.confirmados_10_19_f);
    age_c_f.push(lastData.confirmados_20_29_f);
    age_c_f.push(lastData.confirmados_30_39_f);
    age_c_f.push(lastData.confirmados_40_49_f);
    age_c_f.push(lastData.confirmados_50_59_f);
    age_c_f.push(lastData.confirmados_60_69_f);
    age_c_f.push(lastData.confirmados_70_79_f);
    age_c_f.push(lastData.confirmados_80_plus_f);

    chart2.data = {
      datasets: [
        {
          data: age_c_m,
          backgroundColor: ["#40C0A526"],
          borderColor: ["#40C0A5"],

          label: "Men"
        },
        {
          data: age_c_f,
          backgroundColor: ["#FFC83126"],
          borderColor: ["#FFC831"],

          label: "Women"
        }
      ],
      labels: age_c
    };

    chart3.data = {
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

    let age_o_m = [];
    let age_o_f = [];

    age_o_m.push(lastData.obitos_0_9_m);
    age_o_m.push(lastData.obitos_10_19_m);
    age_o_m.push(lastData.obitos_20_29_m);
    age_o_m.push(lastData.obitos_30_39_m);
    age_o_m.push(lastData.obitos_40_49_m);
    age_o_m.push(lastData.obitos_50_59_m);
    age_o_m.push(lastData.obitos_60_69_m);
    age_o_m.push(lastData.obitos_70_79_m);
    age_o_m.push(lastData.obitos_80_plus_m);

    age_o_f.push(lastData.obitos_0_9_f);
    age_o_f.push(lastData.obitos_10_19_f);
    age_o_f.push(lastData.obitos_20_29_f);
    age_o_f.push(lastData.obitos_30_39_f);
    age_o_f.push(lastData.obitos_40_49_f);
    age_o_f.push(lastData.obitos_50_59_f);
    age_o_f.push(lastData.obitos_60_69_f);
    age_o_f.push(lastData.obitos_70_79_f);
    age_o_f.push(lastData.obitos_80_plus_f);

    chart4.data = {
      datasets: [
        {
          data: age_o_m,
          backgroundColor: ["#40C0A526"],
          borderColor: ["#40C0A5"],

          label: "Men"
        },
        {
          data: age_o_f,
          backgroundColor: ["#FFC83126"],
          borderColor: ["#FFC831"],

          label: "Women"
        }
      ],
      labels: age_c
    };

    chart1.update();
    chart2.update();
    chart3.update();
    chart4.update();
  }

  function initChart() {
    ratio = container_box1.offsetWidth / (container_box1.offsetHeight - 88);

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
        type: "radar",
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
      var ctx3 = canvasElement3.getContext("2d");
      chart3 = new Chart(ctx3, {
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

      var ctx4 = canvasElement4.getContext("2d");
      chart4 = new Chart(ctx4, {
        type: "radar",
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
  .grid-container {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    grid-template-rows: 0.3fr 1fr 0.3fr 1fr;
    grid-gap: 5px;
    height: 40vh;
  }
  .div1 {
    grid-area: 1 / 1 / 2 / 2;
  }
  .chart1 {
    grid-area: 2 / 1 / 3 / 2;
  }
  .chart2 {
    grid-area: 2 / 2 / 3 / 3;
  }
  .div2 {
    grid-area: 3 / 1 / 4 / 2;
  }
  .chart3 {
    grid-area: 4 / 1 / 5 / 2;
  }
  .chart4 {
    grid-area: 4 / 2 / 5 / 3;
  }
</style>

<div class="container-basic container-chart" bind:this={container_box}>

  <div class="container-header">

    <div class="container-header-contents">

      <h5 class="container-title">{box_title}</h5>

    </div>
  </div>
  <div class="grid-container">
    <div class="div1">Comfirm</div>
    <div class="chart1" bind:this={container_box1}>
      <canvas id="myChart" bind:this={canvasElement1} />
    </div>
    <div class="chart2" bind:this={container_box2}>
      <canvas id="myChart" bind:this={canvasElement2} />
    </div>
    <div class="div2">Deaths</div>
    <div class="chart3" bind:this={container_box3}>
      <canvas id="myChart" bind:this={canvasElement3} />
    </div>
    <div class="chart4" bind:this={container_box4}>
      <canvas id="myChart" bind:this={canvasElement4} />
    </div>
  </div>
</div>
