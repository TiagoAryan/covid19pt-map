<script>
  import { onMount } from "svelte";

  export let timelineData;

  let chart;
  let canvasElement;
  let box_title = "Infected Tests";
  let container_box;
  let ratio = 2.4;

  $: chart && timelineData && fillChart();

  onMount(() => {
    initChart();
  });

  async function fillChart() {
    var range_dates = [];
    var interna_data = [];
    var uci_data = [];
    var lab_data = [];
    var n_confi_data = [];
    var vigilan_data = [];
    var suspect_data = [];

    for (const [key, value] of Object.entries(timelineData.data)) {
      range_dates.push(value);
      interna_data.push(timelineData.internados[key]);
      uci_data.push(timelineData.internados_uci[key]);
      lab_data.push(timelineData.lab[key]);
      n_confi_data.push(timelineData.n_confirmados[key]);
      vigilan_data.push(timelineData.vigilancia[key]);
      suspect_data.push(timelineData.suspeitos[key]);
    }

    chart.type = "line";
    chart.options.scales = {
      yAxes: [
        {
          type: "linear", // only linear but allow scale type registration. This allows extensions to exist solely for log scale for instance
          display: true,
          position: "left",
          id: "y-axis-1",
          gridLines: {
            drawOnChartArea: false // only want the grid lines for one axis to show up
          },
          layout: {
            padding: {
              left: 0,
              right: 0,
              top: 0,
              bottom: 20
            }
          },
          legend: {
            display: true,
            labels: {
              boxWidth: 20
            }
          },
          tooltips: {
            titleFontFamily: "Open Sans",
            titleFontSize: 15,
            bodyFontFamily: "Open Sans",
            bodyFontSize: 13
          }
        }
      ]
    };
    chart.data = {
      labels: range_dates,
      datasets: [
        {
          label: "Suspect",
          defaultFontFamily: "Open Sans",
          borderColor: "#007bff",
          backgroundColor: "#40C0A526",
          fill: false,
          data: suspect_data,
          yAxisID: "y-axis-1",
          pointBackgroundColor: "#1E1E21",
          pointBorderWidth: 2,
          borderWidth: 2,
          pointHitRadius: 5,
          pointRadius: 3,
          pointHoverRadius: 12,
          pointHoverBorderWidth: 3
        },
        {
          label: "Internados uci",
          defaultFontFamily: "Open Sans",
          borderColor: "#40C0A5",
          backgroundColor: "#40C0A526",
          fill: false,
          data: uci_data,
          yAxisID: "y-axis-1",
          pointBackgroundColor: "#1E1E21",
          pointBorderWidth: 2,
          borderWidth: 2,
          pointHitRadius: 5,
          pointRadius: 3,
          pointHoverRadius: 12,
          pointHoverBorderWidth: 3
        },
        {
          label: "Lab",
          defaultFontFamily: "Open Sans",
          borderColor: "#FFC831",
          backgroundColor: "#FFC83126",
          fill: false,
          data: lab_data,
          yAxisID: "y-axis-1",
          pointBackgroundColor: "#1E1E21",
          pointBorderWidth: 2,
          borderWidth: 2,
          pointHitRadius: 5,
          pointRadius: 3,
          pointHoverRadius: 12,
          pointHoverBorderWidth: 3
        },
        {
          label: "N Confirmados",
          defaultFontFamily: "Open Sans",
          borderColor: "#FF4E34",
          backgroundColor: "#FF4E3426",
          fill: false,
          data: n_confi_data,
          yAxisID: "y-axis-1",
          pointBackgroundColor: "#1E1E21",
          pointBorderWidth: 2,
          borderWidth: 2,
          pointHitRadius: 5,
          pointRadius: 3,
          pointHoverRadius: 12,
          pointHoverBorderWidth: 3
        },
        {
          label: "Vigilancia",
          defaultFontFamily: "Open Sans",
          borderColor: "#FF4E34",
          backgroundColor: "#FF4E3426",
          fill: false,
          data: vigilan_data,
          yAxisID: "y-axis-1",
          pointBackgroundColor: "#1E1E21",
          pointBorderWidth: 2,
          borderWidth: 2,
          pointHitRadius: 5,
          pointRadius: 3,
          pointHoverRadius: 12,
          pointHoverBorderWidth: 3
        }
      ]
    };

    chart.update();
  }

  function initChart() {
    ratio = container_box.offsetWidth / (container_box.offsetHeight - 88);

    if (chart === undefined) {
      var ctx = canvasElement.getContext("2d");
      chart = new Chart(ctx, {
        type: "line",
        data: {
          labels: [],
          datasets: []
        },
        options: {
          legend: {
            labels: {
              usePointStyle: true
            }
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
            display: false,
            text: "Chart.js Line Chart - Multi Axis"
          },

          scales: {
            yAxes: [
              {
                type: "linear", // only linear but allow scale type registration. This allows extensions to exist solely for log scale for instance
                display: true,
                position: "left",
                id: "y-axis-1",
                gridLines: {
                  drawOnChartArea: false // only want the grid lines for one axis to show up
                },
                layout: {
                  padding: {
                    left: 0,
                    right: 0,
                    top: 0,
                    bottom: 20
                  }
                },
                legend: {
                  display: true,
                  labels: {
                    boxWidth: 20
                  }
                },
                tooltips: {
                  titleFontFamily: "Open Sans",
                  titleFontSize: 15,
                  bodyFontFamily: "Open Sans",
                  bodyFontSize: 13
                }
              }
            ]
          }
        }
      });
    }
  }
</script>

<style>
  .container-chart {
    width: 100%;
    left: 0;
    margin-bottom: 12px;
    display: inline-block;
    opacity: 1;
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
    vertical-align: top;
  }
</style>

<div class="container-basic container-chart" bind:this={container_box}>

  <div class="container-header">

    <div class="container-header-contents">

      <h5 class="container-title">{box_title}</h5>
    </div>
  </div>
  <div class="container-body">
    <canvas id="myChart" bind:this={canvasElement} />
  </div>
</div>
