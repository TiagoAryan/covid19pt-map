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

  $: chart && timelineData && fillChart();
  var data_color = [];


  var hex_red = "FF4E34";
  var hex_yellow ="FFC831";
  var hex_green ="40C0A5";
  var hex_blue ="5172EE";


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


    if (chart_mode == 1) {
      data_color[0] = hex_green;
      data_color[1] = hex_yellow;
      data_color[2] = hex_red;

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
            label: "Recovered",
            defaultFontFamily: "Open Sans",
            borderColor: "#"+data_color[0],
            backgroundColor: "#"+data_color[0]+"26",
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
            borderColor: "#"+data_color[1],
            backgroundColor: "#"+data_color[1]+"26",
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
            borderColor: "#"+data_color[2],
            backgroundColor: "#"+data_color[2]+"26",
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
      data_color[0] = hex_green;
      data_color[1] = hex_yellow;
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
            borderColor: "#"+data_color[0],
            backgroundColor: "#"+data_color[0]+"26",
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
            label: "New Cases",
            defaultFontFamily: "Open Sans",
            borderColor: "#"+data_color[1],
            backgroundColor: "#"+data_color[1],
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
      data_color[0] = hex_yellow;
      data_color[1] = hex_red;

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
            borderColor: "#"+data_color[0],
            backgroundColor: "#"+data_color[0]+"26",
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
            label: "Deaths",
            defaultFontFamily: "Open Sans",
            borderColor: "#"+data_color[1]+"00",
            backgroundColor: "#"+data_color[1]+"99",
            fill: false,
            data: death_per_day,
            yAxisID: "y-axis-2",
            borderWidth: 0,
            pointHitRadius: 5,
            pointRadius: 1,
            pointHoverRadius: 1,
            pointHoverBorderWidth: 2
          }
        ]
      };
    } else if (chart_mode == 4) {
      data_color[0] = hex_blue;
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
            label: "Tests in the Lab",
            defaultFontFamily: "Open Sans",
            borderColor: "#"+data_color[0],
            backgroundColor: "#"+data_color[0]+"26",
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
            mode: "index",
            enabled: false,
            custom: function(tooltipModel) {
              var tooltipEl = document.getElementById("chartjs-tooltip");
              // Create element on first render
              if (!tooltipEl) {
                tooltipEl = document.createElement("div");
                tooltipEl.style.backgroundColor = "rgba(0,0,0,0.9)";
                tooltipEl.style.borderRadius = "4px";
                tooltipEl.style.zIndex = "200";
                tooltipEl.id = "chartjs-tooltip";
                tooltipEl.innerHTML = "<table></table>";
                document.body.appendChild(tooltipEl);
              }

              // Hide if no tooltip
              if (tooltipModel.opacity === 0) {
                tooltipEl.style.opacity = 0;
                return;
              }
              // Set caret Position
              tooltipEl.classList.remove("above", "below", "no-transform");
              if (tooltipModel.yAlign) {
                tooltipEl.classList.add(tooltipModel.yAlign);
              } else {
                tooltipEl.classList.add("no-transform");
              }
              function getBody(bodyItem) {
                return bodyItem.lines;
              }

              // Set Text
              if (tooltipModel.body) {
                var dataindex = tooltipModel.dataPoints[0].datasetIndex;
                var index = tooltipModel.dataPoints[0].index;
                var titleLines = tooltipModel.title || [];
                var bodyLines = tooltipModel.body.map(getBody);
               

                var innerHtml = "<thead>";
                innerHtml +=
                  "<tr><th style='margin-bottom:4px'>  " +
                  titleLines +
                  "</th></tr>";

                innerHtml += "</thead><tbody>";


                for(var i=0; i< bodyLines.length; i++){
                  var res = bodyLines[i][0].split(": ");

            
                  innerHtml +=
                    "<tr><th> " +
                    " <div style=' width:14px;display:inline-block;height:14px; margin-right:6px; border-radius:16px;background-color:#" +data_color[i] +
                    "26; border:2px solid #" + data_color[i] +"'></div>" +
                    "<div style='width:80px; display:inline-block; opacity:0.4; font-weight:400'>"+res[0]+"</div>" +
                    " <div style='width:120px;font-weight:400; display:inline-block; width:40px; text-align:right; font-size:15px'>" +
                    formatNumber(parseInt(res[1])) +
                    "</div>" 
                    "</th></tr>";
                }
               

                innerHtml += "</tbody>";

                var tableRoot = tooltipEl.querySelector("table");
                tableRoot.innerHTML = innerHtml;
              }

              // `this` will be the overall tooltip
              var position = this._chart.canvas.getBoundingClientRect();

              // Display, position, and set styles for font
              tooltipEl.style.opacity = 1;
              tooltipEl.style.position = "absolute";
              tooltipEl.style.left =
                position.left + window.pageXOffset + tooltipModel.caretX + "px";
              tooltipEl.style.top =
                position.top + window.pageYOffset + tooltipModel.caretY + "px";
              tooltipEl.style.fontFamily = tooltipModel._bodyFontFamily;
              tooltipEl.style.fontSize = tooltipModel.bodyFontSize + "px";
              tooltipEl.style.fontStyle = tooltipModel._bodyFontStyle;
              tooltipEl.style.padding =
                tooltipModel.yPadding + "px " + tooltipModel.xPadding + "px";
              tooltipEl.style.pointerEvents = "none";
            }
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

  function formatNumber(num) {
    return num.toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, "$1 ");
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
    <canvas id="myChart" bind:this={canvasElement} />
  </div>
</div>
