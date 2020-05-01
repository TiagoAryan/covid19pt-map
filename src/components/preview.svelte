<script>
  import moment from "moment";
  import { getContext } from "svelte";
  import { fly } from "svelte/transition";

  import Popup from "./Popup.svelte";
  import { s } from "misc";

  const { open } = getContext("simple-modal");

  export let lastData;
  export let timelineData;

  var dt = 0,
    active = 0,
    confirmed = 0,
    recovered = 0,
    deaths = 0,
    recuperados_novos = 0,
    obitos_novos = 0;

  let suspect = 0;
  let internados = 0;
  let tests = 0;
  let internados_grave = 0;

  $: lastData && timelineData && setContent();

  function setContent() {
    if (lastData) {
      dt = lastData.data.split("-");
      confirmed = lastData.confirmados;
      recovered = lastData.recuperados;
      deaths = lastData.obitos;
      active = confirmed - recovered - deaths;

      recuperados_novos =
        timelineData.recuperados[Object.entries(timelineData.data).length - 1] -
        timelineData.recuperados[Object.entries(timelineData.data).length - 2];
      obitos_novos =
        timelineData.obitos[Object.entries(timelineData.data).length - 1] -
        timelineData.obitos[Object.entries(timelineData.data).length - 2];
      console.log(
        timelineData.suspeitos[Object.entries(timelineData.data).length - 1] -
          timelineData.suspeitos[Object.entries(timelineData.data).length - 2]
      );
      suspect = lastData.suspeitos;
      internados = lastData.internados;
      internados_grave = lastData.internados_uci;
      tests = lastData.lab;
    }
  }

  const aboutM = () => {
    open(Popup, { message: "It's a popup!" });
  };
</script>

<style>
  .container-total {
    position: relative;
    display: inline-block;
    margin: 0;
    margin-bottom: 12px;
    width: calc(100% - 10px);
    margin-top: 10px;
    left: 0;
    vertical-align: top;
    padding-bottom: 0px;
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
  .label-blue {
    color: #5172EE;
    background-color: rgba(81, 114, 238, 0.2);
  }

  .container-data-details {
    text-align: left;
  }
  .current-info-container {
    display: block;
    text-align: left;
    margin-top: 35px;
  }
  .container-data-details .col-block,
  .current-info-container .col-block {
    vertical-align:top;
  }
  .current-info-container .col-block label.block {
    display: block;
    margin-bottom: 4px;
    line-height: 0.8rem;
  }
  .container-header label{
    display:block;
  }
  .container-header{
    padding-bottom:0px
    }

  @media (max-width: 1280px) {
    .container-total {
      height: auto;
    }
  }
  @media (max-width: 768px) {
    .container-total {
      height: auto;
    }
    .container-total {
      margin-left: 10px;
      width: calc(100% - 20px);
    }
  }
  @media (max-width: 480px) {
    .container-total {
      margin-bottom: 3px;
    }
  }
</style>

<div class="container-basic container-bottom container-total">

  <div class="container-header">
    <div class="container-header-contents">
      <div class="flag">
        <img src="bandeira.png" alt="flag" />
      </div>
            <h5 class="container-title"> Covid em Portugal</h5>
    
     
      <div style="float:right" class="button secondary" on:click={aboutM}>
        <i class="fas fa-info"></i>
        <p>Sobre</p>
      </div>
       <div style="float:right" class="button secondary">
        <i class="fas fa-newspaper"></i>
        <p>Notícias</p>
      </div>
        <label>—

      Informação obtida em
      <time datetime={moment(dt[2] + dt[1] + dt[0])}>
        {moment(dt[2] + dt[1] + dt[0]).calendar()}
      </time>
      </label>
    </div>

  </div>

  {#if lastData}
    <div class="container-body" style=" width: 100%; margin-bottom: 16px;">
      <div class="container-data-details">
        <div class="col-block">
          <i class="dot dot_green" />
          <label>Recuperados</label>
          <div class="data">
            {s(recovered)}
            <span class="badge badge-light">+ {recuperados_novos}</span>
          </div>
        </div>
        <div class="col-block">
          <i class="dot dot_yellow" />
          <label>Infectados</label>
          <div class="data">
            {s(active)}
            <span class="badge badge-light">
              + {lastData.confirmados_novos}
            </span>
          </div>
        </div>
        <div class="col-block">
          <i class="dot dot_red" />
          <label>Mortes</label>
          <div class="data">
            {s(deaths)}
            <span class="badge badge-light">+ {obitos_novos}</span>
          </div>
        </div>
      </div>
      <div class="progress">
        <div
          class="progress-bar"
          role="progressbar"
          style="width: {recovered ? (recovered * 100) / confirmed : 0}%"
          aria-valuenow={recovered ? (recovered * 100) / confirmed : 0}
          aria-valuemin="0"
          aria-valuemax="100">
          {(recovered ? (recovered * 100) / confirmed : 0).toFixed(0)}%
        </div>
        <div
          class="progress-bar bg-warning"
          role="progressbar"
          style="width: {active ? (active * 100) / confirmed : 0}%"
          aria-valuenow={active ? (active * 100) / confirmed : 0}
          aria-valuemin="0"
          aria-valuemax="100">
          {(active ? (active * 100) / confirmed : 0).toFixed(0)}%
        </div>
        <div
          class="progress-bar bg-danger"
          role="progressbar"
          style="width: {deaths ? (deaths * 100) / confirmed : 0}%"
          aria-valuenow={deaths ? (deaths * 100) / confirmed : 0}
          aria-valuemin="0"
          aria-valuemax="100">
          {(deaths ? (deaths * 100) / confirmed : 0).toFixed(0)}%
        </div>
      </div>
      <label class="progress_label">{s(confirmed)} Casos</label>

      <div class="current-info-container">
        <div class="col-block">
          <label class="block">Testados Hoje</label>
          <div class="label-big label-blue ">
            <h4>{s(tests)}</h4>
          </div>
        </div>
        <div class="col-block">
          <label class="block">Internados</label>
          <div
            class="label-big label-blue
            ">

            <h4>{s(internados)}</h4>
          </div>
        </div>
        <div class="col-block">
          <label class="block">Internados em Estado Grave</label>
          <div
            class="label-big label-blue">

            <h4>{s(internados_grave)}</h4>
          </div>
        </div>

      </div>
    </div>
  {:else}
    <div class="container-body body-healthy">
      <i class="fas fa-heartbeat" />
      <h5>A carregar...</h5>
      <i class="fas fa-heartbeat" />
    </div>
  {/if}
</div>
