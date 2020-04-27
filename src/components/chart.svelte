<script>
  import { onMount } from "svelte";
  import "chartjs-adapter-moment";

  export let timelineData;

  let chart;
  let chart_mode = 1;
  let canvasElement;
  let box_title = "Infected Count";
  let btn_text = "Growth Evolution";
  let btn_icon = "chart-line";
  let container_box;
  let ratio = 2.4;

  let suspect = 0;
  let internados = 0;
  let tests = 0;

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
    var growth_data = [];
    var death_per_day = [];
    var growth_per_day = [];
    var interna_data = [];
    var uci_data = [];
    var lab_data = [];

    let k = 0;
    var previous_d = 0;

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
        let death = parseInt(
          timelineData.obitos[key] - timelineData.obitos[key - 1]
        );

        death_per_day.push(death);
        growth_data.push(growth);
      } else {
        death_per_day.push(0);
        growth_data.push(0);
      }
      k++;

      growth_per_day.push(timelineData.confirmados_novos[key]);

      interna_data.push(timelineData.internados[key]);
      uci_data.push(timelineData.internados_uci[key]);
      lab_data.push(timelineData.lab[key]);
    }

    suspect = suspect_data[suspect_data.length - 1];
    internados = uci_data[uci_data.length - 1];
    tests = lab_data[lab_data.length - 1];

    if (chart_mode == 1) {
      chart.type = "line";
      chart.options.scales = {
        xAxes: [
          {
            type: "time",
            time: {
              parser: "DD/MM/YYYY",
              tooltipFormat: "D MMM YYYY",
              displayFormats: {
                day: "D MMM"
              }
            }
          }
        ],
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
          /*
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
            pointRadius: 2,
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          },
          */
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
            pointRadius: 2,
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
            pointRadius: 2,
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
            pointRadius: 2,
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          }
        ]
      };
    } else if (chart_mode == 2) {
      chart.type = "bar";
      chart.options.scales = {
        xAxes: [
          {
            type: "time",
            time: {
              parser: "DD/MM/YYYY",
              tooltipFormat: "D MMM YYYY",
              displayFormats: {
                day: "D MMM"
              }
            }
          }
        ],
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
    } else if (chart_mode == 3) {
      chart.type = "bar";
      chart.options.scales = {
        xAxes: [
          {
            type: "time",
            time: {
              parser: "DD/MM/YYYY",
              tooltipFormat: "D MMM YYYY",
              displayFormats: {
                day: "D MMM"
              }
            }
          }
        ],
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
            label: "ICU interneds",
            defaultFontFamily: "Open Sans",
            borderColor: "#40C0A5",
            backgroundColor: "#40C0A526",
            fill: false,
            data: uci_data,
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
            label: "Death per Day",
            defaultFontFamily: "Open Sans",
            borderColor: "#FFC831",
            backgroundColor: "#FFC831",
            fill: false,
            data: death_per_day,
            yAxisID: "y-axis-2",
            borderWidth: 1,
            pointHitRadius: 5,
            pointRadius: 1,
            pointHoverRadius: 1,
            pointHoverBorderWidth: 2
          }
        ]
      };
    } else if (chart_mode == 4) {
      chart.type = "line";
      chart.options.scales = {
        xAxes: [
          {
            type: "time",
            time: {
              parser: "DD/MM/YYYY",
              tooltipFormat: "D MMM YYYY",
              displayFormats: {
                day: "D MMM"
              }
            }
          }
        ],
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
            pointRadius: 1,
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
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

  function changeChart(chart) {
    if (chart == 1) {
      chart_mode = 1;
      btn_text = "Infected Count";
    } else if (chart == 2) {
      chart_mode = 2;
      box_title = "Infected Count";
    } else if (chart == 3) {
      chart_mode = 3;
      box_title = "ICU";
    } else if (chart == 4) {
      chart_mode = 4;
      box_title = "Lab Results";
    }
    fillChart();
  }
</script>

<style>
  .container-chart {
    width: calc(100% - 10px);
    height: calc(50vh - 110px);
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

  .label-big h4 {
    display: inline-block;
    margin: 0;
    font-weight: 600;
  }
  .label-big {
    padding: 6px 12px;
    border-radius: 6px;
    display: inherit;
  }
  .label-yellow {
    color: #ffc831;
    background-color: rgba(255, 200, 49, 0.2);
  }
  .label-red {
    color: #ff4e34;
    background-color: rgba(255, 78, 52, 0.2);
  }
  .label-green {
    color: #40c0a5;
    background-color: rgba(64, 192, 165, 0.2);
  }
  .col-block label {
    display: block;
    margin-bottom: 4px;
    line-height: 0.8rem;
  }
  @media (max-width: 768px) {
    .container-chart {
      margin-left: 10px;
      width: calc(100% - 20px);
    }
  }
</style>

<div class="container-basic container-chart" bind:this={container_box}>

  <div class="container-header">

    <div class="container-header-contents">

      <h5 class="container-title">{box_title}</h5>
      {#if chart_mode != 4}
        <div
          style="float:right"
          class="button secondary"
          on:click={() => changeChart(4)}>
          <i class="fas fa-chart-line" />
          <p>Lab Results</p>
        </div>
      {/if}
      {#if chart_mode != 3}
        <div
          style="float:right"
          class="button secondary"
          on:click={() => changeChart(3)}>
          <i class="fas fa-chart-line" />
          <p>ICU</p>
        </div>
      {/if}
      {#if chart_mode != 2}
        <div
          style="float:right"
          class="button secondary"
          on:click={() => changeChart(2)}>
          <i class="fas fa-user-friends" />
          <p>Growth Evolution</p>
        </div>
      {/if}
      {#if chart_mode != 1}
        <div
          style="float:right"
          class="button secondary"
          on:click={() => changeChart(1)}>
          <i class="fas fa-chart-line" />
          <p>Infected Count</p>
        </div>
      {/if}
    </div>
  </div>

  <div class="container-body">
    <div class="col-block">
      <label>Tested Today</label>
      <div class="label-big label-yellow ">
        <h4>{tests}</h4>
      </div>
    </div>
    <div class="col-block">
      <label>Internados Today</label>
      <div
        class="label-big {internados < 1000 ? 'label-red' : ''}
        {internados > 500 && internados <= 1000 ? 'label-yellow' : ''}
        {internados <= 500 ? 'label-green' : ''}
        ">

        <h4>{internados}</h4>
      </div>
    </div>

    <canvas id="myChart" bind:this={canvasElement} />
  </div>
</div>
