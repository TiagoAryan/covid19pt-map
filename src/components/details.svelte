<script>
  export let timelineData;

  var dt = 0,
    active = 0,
    confirmed = 0,
    recovered = 0,
    deaths = 0;

  $: timelineData, setContent();

  function setContent() {
    if (timelineData) {
      // console.log(timelineData);
    }
  }
</script>

<style>

</style>
