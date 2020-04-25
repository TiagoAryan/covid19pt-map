<script>
  import { onMount } from "svelte";
  import 'chartjs-plugin-labels';

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
      labels: ["Men", "Woman"],
      datasets: [
        {
          data: [lastData.confirmados_m, lastData.confirmados_f],
          backgroundColor: ['rgba(255,221,128, .2)','rgba(255,200,49, .2)'],
          borderColor: ['rgba(255,221,128, 1)','rgba(255,200,49, 1)'],
          hoverBackgroundColor: ['rgba(255,221,128, .4)','rgba(255,200,49, .4)'],
          borderWidth:2,
          hoverBorderWidth:3

        }
      ]
    };


    chart2.data = {
      labels: ["Men", "Woman"],
      datasets: [
        {
          data: [lastData.obitos_m, lastData.obitos_f],
          backgroundColor: ['rgba(255,148,143, .2)', 'rgba(255,78,52, .2)'],
          borderColor: ['rgba(255,148,143, 1)', 'rgba(255,78,52, 1)'],
          hoverBackgroundColor: ['rgba(255,148,143, .4)', 'rgba(255,78,52, .4)'],
          borderWidth:2,
          hoverBorderWidth:3

        }
      ]
    };

    chart1.update();
    chart2.update();
  }

  function initChart() {
    ratio = (container_box1.offsetWidth-30) / (container_box1.offsetHeight-40);

    if (chart1 === undefined) {
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
      var ctx2 = canvasElement2.getContext("2d");
      chart2 = new Chart(ctx2, {
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
                  "<div style='margin-right:12px;display:inline-block; opacity:0.4; font-weight:400'>"+gender+"</div>"+
                  " <div style='font-weight:400; display:inline-block;  text-align:right; font-size:15px'>" +
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
  function formatNumber(num) {
  return num.toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, '$1 ')
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

  .grid {
    display: grid;
    grid-template-columns: repeat(12, minmax(0, 1fr));
    grid-gap: 16px;
  }
  .grid-pie {
    grid-column: span 12;
  }
  @media (max-width: 768px) {
    .container-chart {
      margin-left:10px;
      width: calc(100% - 20px);

    }
    .grid-pie {
      grid-column: span 6;
    }
    .container-chart {
      height:50vh;
      min-height:300px;
      margin-bottom:12px;
    }
  }
  @media (max-width: 480px) {
   
  }
</style>

<div class="container-basic container-chart" bind:this={container_box}>

  <div class="container-header">

    <div class="container-header-contents">

      <h5 class="container-title">{box_title}</h5>

    </div>
  </div>
  <div class="container-body">
    <div class="grid">
      <div class="grid-pie">
        <div class="div1">Confirmed</div>
        <div class="chart-box" bind:this={container_box1}>
          <canvas id="myChart" bind:this={canvasElement1} />
        </div>
      </div>
      <div class="grid-pie">
        <div class="div2">Deaths</div>
        <div class="chart-box" bind:this={container_box2} >
          <canvas id="myChart" bind:this={canvasElement2} />
      </div>
    </div>
  </div>
  </div>
</div>
