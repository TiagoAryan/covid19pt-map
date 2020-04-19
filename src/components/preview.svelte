<script>
  import { s } from "misc";
  import moment from "moment";
  import "moment/locale/pt";

  export let lastDdata;

  var dt = 0,
    active = 0,
    confirmed = 0,
    recovered = 0,
    deaths = 0;

  moment.locale("pt");

  $: lastDdata, setContent();

  function setContent() {
    if (lastDdata) {
      let data = lastDdata[0].attributes;
      console.log(data);

      dt = data.CreationDate;
      active = data.CasosActivos;
      confirmed = data.casosconfirmados;
      recovered = data.recuperados;
      deaths = data.nrobitos;
    }
  }
</script>

<style>
  .container-total {
    position: relative;
    display: inline-block;
    margin: 0;
    margin-bottom: 12px;
    width: 100%;
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
      Dados de {moment(dt).format('LLL')}
    </div>

  </div>

  {#if lastDdata}
    <div
      class="container-body"
      style=" width: 100%; padding-bottom: 28px; margin-bottom: 16px;">
      <div class="container-data-details">
        <div class="col-block">
          <i class="dot dot_green" />
          <label>Recovered</label>
          <div class="data">{s(recovered)}</div>
        </div>
        <div class="col-block">
          <i class="dot dot_yellow" />
          <label>Infected</label>
          <div class="data">{s(active)}</div>
        </div>
        <div class="col-block">
          <i class="dot dot_red" />
          <label>Deaths</label>
          <div class="data">{s(deaths)}</div>
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
