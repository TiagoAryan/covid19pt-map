<script>
  import moment from "moment";

  let data;

  getContent();

  async function getContent() {
    /*  const response = await fetch(
      "https://newsapi.org/v2/everything?q=coronavirus%20virus%20world%20covid%2019&language=en&sources=al-jazeera-english,associated-press,bbc-news,breitbart-news,independent,time&sortBy=publishedAt&apiKey=db91ea828da64f4fa54ee12fdf7ea095"
    ); . articles */
    const response = await fetch(
      "https://cors-anywhere.herokuapp.com/https://www.publico.pt/api/list/search?query=coronavirus"
    );
    data = await response.json();
  }
</script>

<style>
  .container {
    width: calc(100% - 10px);
    height: calc(100vh - 210px);
    position: relative;
  }
  .container-body {
    position: relative;
    height: calc(100% - 60px);
  }
  figure {
    position: static;
    display: block;
    bottom: auto;
    left: auto;
    z-index: 1;
    float: right;
    overflow: hidden;
    border-radius: 8px;
  }
  label {
    display: inline-block;
    width: 100%;
    margin-bottom: 4px;
    font-size: 0.7rem;
  }
  h5 {
    display: inline-block;
    width: auto;
    font-weight: 300;
    margin: 0px 0px 10px 0px;
  }
  a {
    color: inherit;
    text-decoration: inherit;
  }
  .news {
    display: inline-block;
    width: 75%;
    vertical-align: top;
  }
  img {
    object-fit: cover;
    width: 100px;
    height: 100px;
  }
</style>

<svelte:head>
  <title>Covid 19 News</title>
</svelte:head>
<div class="container-basic container">

  <div class="container-header">

    <div class="container-header-contents">

      <h5 class="container-title">News</h5>

    </div>
  </div>
  {#if data}
    <div class="block-news">
      {#each data as item, i}
        <a href={item.fullUrl} target="_blank" class="more">
          <div class="container-basic">
            <div class="container-body">
              <figure>
                <img src={item.multimediaPrincipal} alt="img" />
              </figure>
              <div class="news">
                <h5>
                  {@html item.lead}
                </h5>
                <label>
                  Público
                  <time datetime={item.data}>
                    {moment(item.data).fromNow()}
                  </time>
                </label>
                <p class="d-none d-sm-none d-sx-none">
                  {@html item.descricao}
                </p>
              </div>
              <p class="d-sm-block d-sx-block">
                {@html item.descricao}
              </p>
            </div>
          </div>
        </a>
      {/each}
    </div>
  {:else}
    <div class="pane-loading">
      <p>Getting Data</p>
      <div class="lds-roller">
        <div />
        <div />
        <div />
        <div />
        <div />
        <div />
        <div />
        <div />
      </div>
    </div>
  {/if}
</div>
