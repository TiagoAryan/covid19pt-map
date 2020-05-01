<script>
  import moment from "moment";
  import "moment/locale/pt";
  import { getContext } from "svelte";
  import { fly } from "svelte/transition";
  import PopupA from "./PopupA.svelte";
  import PopupN from "./PopupN.svelte";
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
    open(PopupA);
  };
  const newsN = () => {
    open(PopupN);
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
    color: #5172ee;
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
    vertical-align: top;
  }
  .current-info-container .col-block label.block {
    display: block;
    margin-bottom: 4px;
    line-height: 0.8rem;
  }
  .container-header label {
    display: block;
  }
  .container-header {
    padding-bottom: 0px;
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
        <img
          src="data:image/png;base64,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"
          alt="flag" />
      </div>
      <h5 class="container-title">COVID-19 em Portugal</h5>

      <div style="float:right" class="button secondary" on:click={aboutM}>
        <i class="fas fa-info" />
        <p>Sobre</p>
      </div>
      <div style="float:right" class="button secondary" on:click={newsN}>
        <i class="fas fa-newspaper" />
        <p>Notícias</p>
      </div>
      <label>
        — Informação obtida
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
          <div class="label-big label-blue ">

            <h4>{s(internados)}</h4>
          </div>
        </div>
        <div class="col-block">
          <label class="block">Internados em Estado Grave</label>
          <div class="label-big label-blue">

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
