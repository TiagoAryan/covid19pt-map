<script>
  import { onMount } from "svelte";

  export let lastData;

  let container_box;
  let box_title = "Faixas Etárias — Mortes";
  let btn_text = "Infectados";
  let btn_icon = "user-friends";

  let chart1;
  let chart_r2;

  let container_box1;
  let container_box_r2;

  let canvasElement1;
  let canvasElement_r2;

  let ratio = 2.4;
  var chart_mode = false;
  var data_men, data_woman, data_color, data_pie;
  var data_color = { men: {}, woman: {} };

  $: chart_r2 && lastData && fillChart();

  onMount(() => {
    initChart();
  });

  async function fillChart() {
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

    if (chart_mode) {
      data_men = age_c_m;
      data_woman = age_c_f;
      data_color.men = {
        r: 255,
        g: 221,
        b: 128
      };
      data_color.woman = {
        r: 255,
        g: 200,
        b: 49
      };

      data_pie= [lastData.confirmados_m, lastData.confirmados_f];

    } else {
      data_men = age_o_m;
      data_woman = age_o_f;
      data_color.men = {
        r: 255,
        g: 148,
        b: 143
      };
      data_color.woman = {
        r: 255,
        g: 78,
        b: 52
      };
      data_pie= [lastData.obitos_m, lastData.obitos_f];
    }
    chart1.data = {
      labels: ["Homens", "Mulheres"],
      datasets: [
        {
          data: data_pie,
          backgroundColor: ["rgba(" +
            data_color.men.r +
            "," +
            data_color.men.g +
            "," +
            data_color.men.b +
            ", .2)","rgba(" +
            data_color.woman.r +
            "," +
            data_color.woman.g +
            "," +
            data_color.woman.b +
            ", .2)"],
          borderColor: ["rgba(" +
            data_color.men.r +
            "," +
            data_color.men.g +
            "," +
            data_color.men.b +
            ", 1)","rgba(" +
            data_color.woman.r +
            "," +
            data_color.woman.g +
            "," +
            data_color.woman.b +
            ")"],
          hoverBackgroundColor: ["rgba(" +
            data_color.men.r +
            "," +
            data_color.men.g +
            "," +
            data_color.men.b +
            ", .4)","rgba(" +
            data_color.woman.r +
            "," +
            data_color.woman.g +
            "," +
            data_color.woman.b +
            ", .4)"],
          borderWidth:2,
          hoverBorderWidth:3

        }
      ]
    };
    chart_r2.data = {
      labels: age_c,
      datasets: [
        {
          label: "Homens",
          backgroundColor:
            "rgba(" +
            data_color.men.r +
            "," +
            data_color.men.g +
            "," +
            data_color.men.b +
            ", .2)",
          borderColor:
            "rgba(" +
            data_color.men.r +
            "," +
            data_color.men.g +
            "," +
            data_color.men.b +
            ", 1)",
          hoverBackgroundColor:
            "rgba(" +
            data_color.men.r +
            "," +
            data_color.men.g +
            "," +
            data_color.men.b +
            ",.4)",
          borderWidth: 2,
          hoverBorderWidth: 3,
          stack: "Stack 0",
          data: data_men
        },
        {
          label: "Mulheres",
          backgroundColor:
            "rgba(" +
            data_color.woman.r +
            "," +
            data_color.woman.g +
            "," +
            data_color.woman.b +
            ", .2)",
          borderColor:
            "rgba(" +
            data_color.woman.r +
            "," +
            data_color.woman.g +
            "," +
            data_color.woman.b +
            ", 1)",
          hoverBackgroundColor:
            "rgba(" +
            data_color.woman.r +
            "," +
            data_color.woman.g +
            "," +
            data_color.woman.b +
            ", .4)",
          borderWidth: 2,
          hoverBorderWidth: 3,
          stack: "Stack 0",
          data: data_woman
        }
      ]
    };
    chart1.update();
    chart_r2.update();
  }

  function initChart() {
    ratio = container_box_r2.offsetWidth / container_box_r2.offsetHeight;
    data_color.men = {
      r: 255,
      g: 221,
      b: 128
    };
    data_color.woman = {
      r: 255,
      g: 200,
      b: 49
    };
    if (chart_r2 === undefined) {
      var ctx_r2 = canvasElement_r2.getContext("2d");
      chart_r2 = new Chart(ctx_r2, {
        type: "bar",
        data: {},

        options: {
          plugins: {
            labels: {
              render: "",
              fontColor: [
                "transparent",
                "transparent",
                "transparent",
                "transparent",
                "transparent",
                "transparent",
                "transparent",
                "transparent",
                "transparent"
              ],
              precision: 0
            }
          },
          scales: {
            xAxes: [
              {
                stacked: true,
                scaleLabel: {
                  display: true,
                  labelString: ""
                }
              }
            ],
            yAxes: [
              {
                stacked: true,
                ticks: {
                  beginAtZero: true
                }
              }
            ]
          },
          legend: {
            labels: {
              usePointStyle: true
            },
            display: false
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

                var num_m = parseInt(bodyLines[0][0].replace("Homens: ", ""));
                var num_w = parseInt(bodyLines[1][0].replace("Mulheres: ", ""));
                var total = num_m + num_w;

                var percentage_m = parseInt((num_m * 100) / total);
                var percentage_w = parseInt((num_w * 100) / total);

                var innerHtml = "<thead>";
                innerHtml +=
                  "<tr><th style='margin-bottom:4px'> Faixa Etária — " +
                  titleLines +
                  "</th></tr>";

                innerHtml += "</thead><tbody>";

                innerHtml +=
                  "<tr><th> " +
                  " <div style=' width:14px;display:inline-block;height:14px; margin-right:6px; border-radius:16px;background-color:rgba(" +
                  data_color.men.r +
                  "," +
                  data_color.men.g +
                  "," +
                  data_color.men.b +
                  ", .2); border:2px solid rgba(" +
                  data_color.men.r +
                  "," +
                  data_color.men.g +
                  "," +
                  data_color.men.b +
                  ", 1)'></div>" +
                  "<div style='width:80px; display:inline-block; opacity:0.4; font-weight:400'>Homens</div>" +
                  " <div style='font-weight:400; display:inline-block; width:40px; text-align:right; font-size:15px'>" +
                  formatNumber(num_m) +
                  "</div>" +
                  " <div style='font-weight:400; opacity:0.6; display:inline-block; width:30px; text-align:right; font-size:11px'>" +
                  percentage_m +
                  "%</div>" +
                  "</th></tr>";
                innerHtml +=
                  "<tr><th>" +
                  " <div style=' width:14px;display:inline-block;height:14px; margin-right:4px; border-radius:16px;background-color:rgba(" +
                  data_color.woman.r +
                  "," +
                  data_color.woman.g +
                  "," +
                  data_color.woman.b +
                  ", .2); border:2px solid rgba(" +
                  data_color.woman.r +
                  "," +
                  data_color.woman.g +
                  "," +
                  data_color.woman.b +
                  ", 1)'></div>" +
                  " <div style='width:80px; display:inline-block; opacity:0.4; font-weight:400'>Mulheres</div>" +
                  " <div style='font-weight:400; display:inline-block; width:40px; text-align:right; font-size:15px'>" +
                  formatNumber(num_w) +
                  "</div>" +
                  " <div style='font-weight:400; opacity:0.6; display:inline-block; width:30px; text-align:right; font-size:11px'>" +
                  percentage_w +
                  "%</div>" +
                  "</th></tr>";

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
          showLines: false,
          title: {
            display: false
          }
        }
      });
      ratio = (container_box1.offsetWidth) / (container_box1.offsetHeight-40);

      var ctx1 = canvasElement1.getContext("2d");
      chart1 = new Chart(ctx1, {
        type: "pie",
        data: {
          labels: [],
          datasets: []
        },
        options: {
          plugins:{
              labels: {
                render: 'percentage',
                fontColor: ['white', 'white'],
                precision: 0,
                arc: false,
              }
          },
          legend: {
            labels: {
              usePointStyle: true
            },
            display: true,
            position:"bottom"

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
                var gender="Men";
                if( bodyLines[0][0].includes("Woman")){
                  gender="Woman";

                }
                var num_m=parseInt(bodyLines[0][0].replace(gender+": ", ""));

                var innerHtml = "<thead>";

                innerHtml += "</thead><tbody>";

                innerHtml +=
                  "<tr><th> "+
                  "<div style='margin-right:12px; display:inline-block; opacity:0.4; font-weight:400'>"+gender+"</div>"+
                  " <div style='font-weight:400; display:inline-block; text-align:right; font-size:15px'>" +
                  formatNumber(num_m) +
                  "</div>"+
                  "</th></tr>";
               

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

      box_title = "Faixas Etárias — Mortes";
      btn_text = "Infectados";
      btn_icon = "user-friends";
    } else {
      chart_mode = true;
      fillChart();
      box_title = "Faixas Etárias — Infectados";
      btn_text = "Mortes";
      btn_icon = "chart-line";
    }
  }
  function formatNumber(num) {
    return num.toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, "$1 ");
  }
</script>

<style>
  .container-chart {
    width: calc( 100% - 10px);
    height: calc(50vh - 190px);
  }
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
  .chart4,
  .chart2 {
    height: 90%;
  }
  .container-body {
    height: calc(100% - 40px);
    position: relative;
    padding: 15px 24px;
  }

  .grid {
    display: grid;
    grid-template-columns: repeat(12, minmax(0, 1fr));
    grid-gap: 16px;

        height: 100%;
    position: relative;
  }
  .grid-pie {
    grid-column: span 4;
        height: 100%;

  }
  .grid-radar {
    grid-column: span 8;
        height: 100%;
  }
  @media (max-width: 768px) {
    .container-chart {
      margin-left: 10px;
      width: calc(100% - 20px);
    }
    .container-chart {
      height: auto;
    }
    .grid-pie {
    grid-column: span 12;
    }
    .grid-radar {
      grid-column: span 12;
    }
  }
  @media (max-width: 480px) {
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
    <div class="grid">
      <div class="grid-radar">
        <div class="chart2" bind:this={container_box_r2}>
          <canvas id="myChart" bind:this={canvasElement_r2} />
        </div>
      </div>
      <div class="grid-pie">
        <div class="chart-box" bind:this={container_box1}>
          <canvas id="myChart" bind:this={canvasElement1} />
        </div>
      </div>
    </div>
  </div>
</div>
