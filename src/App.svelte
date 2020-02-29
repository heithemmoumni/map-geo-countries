<script>
  import * as d3 from "d3";
  import { onMount } from "svelte";
  let mountPoint;

  async function renderMap() {
    const width = mountPoint.offsetWidth.toFixed(),
      height = mountPoint.offsetHeight.toFixed();
    console.log(width, height);

    let mercatorScale,
      w = window.screen.width;
    if (w > 1366) {
      mercatorScale = 11000;
    } else if (w <= 1366 && w > 480) {
      mercatorScale = 9000;
    } else {
      mercatorScale = 6000;
    }

    let projection = await d3
      .geoMercator()
      .center([10, 36])
      .scale(mercatorScale / 2)
      .translate([width / 2, height / 3.5]);

    let path = await d3.geoPath(projection);
    var svg = d3
      .select("#svg")
      .attr("width", width)
      .attr("height", height)
      .attr("viewBox", `0 0 ${width} ${height}`);

    var url = "tunisie.geojson";

    await d3
      .json(url)
      .then(function(geometry) {
        console.log(geometry);
        svg
          .selectAll("path")
          .data(geometry.features)
          .enter()
          .append("path")
          .attr("d", path)
          .attr("id", function(_d, i) {
            console.log(_d, i);
            return i;
          });
      })
      .catch(function(error) {
        if (error) throw error;
      });
  }

  onMount(() => {
    renderMap();
  });
</script>

<style>

</style>

<div class="content-map" ref="map" bind:this={mountPoint}>
  <div id="map">
    <svg
      id="svg"
      xmlns="http://www.w3.org/2000/svg"
      preserveAspectRatio="xMidYMid meet">
      <path />
    </svg>
  </div>
</div>
