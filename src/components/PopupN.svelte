<script>
  import moment from "moment";

  let data;

  getContent();

  async function getContent() {
    const response = await fetch(
      "https://cors-anywhere.herokuapp.com/https://www.publico.pt/api/list/search?query=coronavirus"
    );
    data = await response.json();
  }
</script>

<style>
  .container-body {
    position: relative;
    display: block;
    width: 100%;
  }
  .container-body p {
    width: 70%;
    display: inline-block;
    margin: 0;
    font-size: 0.9rem;
    color: whitesmoke;
    line-height: 0.8rem;
    vertical-align: top;
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
  h3 {
    font-size: 1.3rem;
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

  @media (max-width: 768px) {
    .container-body {
      padding: 0;
    }
    .container-body p {
      width: 100%;
    }
    .more:first-of-type img {
      width: 100%;
      height: 120px;
    }
    .more:first-of-type .news {
      width: 100%;
    }

    img {
      width: 80px;
      height: 80px;
    }
  }
</style>

<div>
  <div class="container-header">
    <h5>Notícias</h5>

  </div>
  <div class="container-body">
    <div class="block-news">

      {#if data}
        {#each data as item, i}
          <a href={item.fullUrl} target="_blank" class="more">
            <div class="container-basic" style="box-shadow: 0px 0px 0px black;">
              <div class="container-body" style="padding: 0;">
                <figure>
                  <img src={item.multimediaPrincipal} alt="img" class="img" />
                </figure>
                <div class="news">
                  <h3>
                    {@html item.lead}
                  </h3>
                  <label>
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
          <hr />
        {/each}
      {:else}Loading...{/if}
    </div>
  </div>
</div>
