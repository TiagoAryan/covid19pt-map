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
      Information for Portugal from
      <time datetime={moment(dt[2] + dt[1] + dt[0])}>
        {moment(dt[2] + dt[1] + dt[0]).calendar()}
      </time>
      <div style="float:right" class="button secondary" on:click={aboutM}>
        <p>About</p>
      </div>
    </div>

  </div>

  {#if lastData}
    <div
      class="container-body"
      style=" width: 100%; padding-bottom: 28px; margin-bottom: 16px;">
      <div class="container-data-details">
        <div class="col-block">
          <i class="dot dot_green" />
          <label>Recovered</label>
          <div class="data">
            {s(recovered)}
            <span class="badge badge-light">+ {recuperados_novos}</span>
          </div>
        </div>
        <div class="col-block">
          <i class="dot dot_yellow" />
          <label>Infected</label>
          <div class="data">
            {s(active)}
            <span class="badge badge-light">
              + {lastData.confirmados_novos}
            </span>
          </div>
        </div>
        <div class="col-block">
          <i class="dot dot_red" />
          <label>Deaths</label>
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
      <label class="progress_label">{s(confirmed)} Cases</label>
    </div>
  {:else}
    <div class="container-body body-healthy">
      <i class="fas fa-heartbeat" />
      <h5>Loading...</h5>
      <i class="fas fa-heartbeat" />
    </div>
  {/if}
</div>
