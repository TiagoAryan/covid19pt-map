<script>
  import { onMount } from "svelte";

  export let timelineData;

  let chart;
  let chart_mode = true;
  let canvasElement;
  let box_title = "Infected Count";
  let btn_text = "Growth Evolution";
  let btn_icon = "chart-line";
  let container_box;
  let ratio = 2.4;

  $: chart && timelineData && fillChart();

  onMount(() => {
    initChart();
  });

  async function fillChart() {
    var active_data = [];
    var recovered_data = [];
    var deaths_data = [];
    var range_dates = [];
    var suspect_data = [];

    let k = 0;
    var previous_d = 0;

    var growth_data = [];
    var growth_per_day = [];

    for (const [key, value] of Object.entries(timelineData.data)) {
      range_dates.push(value);
      deaths_data.push(timelineData.obitos[key]);
      recovered_data.push(timelineData.recuperados[key]);
      active_data.push(
        timelineData.confirmados[key] -
          timelineData.obitos[key] -
          timelineData.recuperados[key]
      );
      suspect_data.push(timelineData.suspeitos[key]);

      if (k > 0) {
        let growth = parseInt(
          ((timelineData.confirmados[key] - timelineData.confirmados[key - 1]) *
            100) /
            timelineData.confirmados[key - 1]
        );
        growth_data.push(growth);
      } else {
        growth_data.push(0);
      }
      k++;

      growth_per_day.push(timelineData.confirmados_novos[key]);
    }

    if (chart_mode) {
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
            label: "Recovered",
            defaultFontFamily: "Open Sans",
            borderColor: "#40C0A5",
            backgroundColor: "#40C0A526",
            fill: false,
            data: recovered_data,
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
            label: "Active",
            defaultFontFamily: "Open Sans",
            borderColor: "#FFC831",
            backgroundColor: "#FFC83126",
            fill: false,
            data: active_data,
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
            label: "Deaths",
            defaultFontFamily: "Open Sans",
            borderColor: "#FF4E34",
            backgroundColor: "#FF4E3426",
            fill: false,
            data: deaths_data,
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
    } else {
      chart.type = "bar";
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
          },
          {
            type: "linear", // only linear but allow scale type registration. This allows extensions to exist solely for log scale for instance
            display: true,
            position: "right",
            id: "y-axis-2",
            gridLines: {
              drawOnChartArea: false // only want the grid lines for one axis to show up
            }
          }
        ]
      };
      chart.data = {
        labels: range_dates,
        datasets: [
          {
            type: "line",
            label: "Growth %",
            defaultFontFamily: "Open Sans",
            borderColor: "#40C0A5",
            backgroundColor: "#40C0A526",
            fill: false,
            data: growth_data,
            yAxisID: "y-axis-1",
            pointBackgroundColor: "#1E1E21",
            pointBorderWidth: 2,
            borderWidth: 2,
            pointHitRadius: 3,
            pointRadius: 1,
            pointHoverRadius: 4,
            pointHoverBorderWidth: 2
          },

          {
            type: "bar",
            label: "New Cases per Day",
            defaultFontFamily: "Open Sans",
            borderColor: "#FFC831",
            backgroundColor: "#FFC831",
            fill: false,
            data: growth_per_day,
            yAxisID: "y-axis-2",
            borderWidth: 1,
            pointHitRadius: 5,
            pointRadius: 1,
            pointHoverRadius: 1,
            pointHoverBorderWidth: 2
          }
        ]
      };
    }

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
    width: 100% ;
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
  .container-chart.country {
    height: calc(100vh - 348px);
  }
  .container-chart.world {
    height: calc(50% - 12px);
  }
  @media (max-width: 768px) {
    .container-chart.country {
      height: 60vh;
    }
    .container-chart.world {
      height: 60vh;
    }
  }

  @media (max-width: 480px) {
    .container-chart.country {
      height: 50vh;
    }
    .container-chart.world {
      height: 50vh;
    }
  }
</style>

<div class="container-basic container-chart" bind:this={container_box}>

  <div class="container-header">

    <div class="container-header-contents">

      <h5 class="container-title">{box_title}</h5>
      <div
        style="float:right"
        class="button secondary"
        on:click={() => changeChart()}>
        <i class="fas fa-{btn_icon}" />
        <p>{btn_text}</p>
      </div>
    </div>
  </div>
  <div class="container-body">
    <canvas id="myChart" bind:this={canvasElement} />
  </div>
</div>
