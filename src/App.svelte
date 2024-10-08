<script>
  import data from "$data/data.js";
  import Line from "$components/Line.svelte";
  import Tooltip from "$components/Tooltip.svelte";
  import HoverEvents from "$components/HoverEvents.svelte";
  import * as d3 from 'd3';
  
  const margin = { top: 15, right: 20, bottom: 30, left: 50 };
  let width = 600;
  let height = 400;
  $: innerWidth = width - margin.left - margin.right;
  let innerHeight = height - margin.top - margin.bottom;

  const parseDate = d3.timeParse("%b-%y");

  data.forEach(d => {
    d.Fecha = parseDate(d.Fecha);
  });

  $: xScale = d3.scaleUtc()
    .domain(d3.extent(data, (d) => (d.Fecha)))
    .range([0, innerWidth]);

  const yScale = d3.scaleLinear()
    .domain([0, d3.max(data, d => d.Asequibilidad)])
    .range([innerHeight, 0]);

  $: lineAsequibilidad = d3.line()
  .curve(d3.curveBasis)
    .x((d) => xScale(d.Fecha))
    .y((d) => yScale(d.Asequibilidad));
  const formatYear = d3.timeFormat("%Y");

  let maxDate = data[data.length - 1].Fecha;
    
  let hoveredDate = maxDate;

</script>

<div class="main" bind:clientWidth={width}>
  <h1 {width} {height}>El palo hipotecario a los 'boomers' para convertirse en propietarios</h1>
  <p {width} {height} class="subtitulo">% de la renta anual disponible del hogar mediano destinado al pago del primer año de una hipoteca al 80%</p>
  <div class="chart-container">
    <svg {width} {height}>
      <g class="inner-chart" transform="translate({margin.left}, {margin.top})">
        {#each [0,10,30,50,70] as tick, i}
        {#if i !== 0}
            <g class="tick" transform="translate(0, {yScale(tick)})">
                <text text-anchor="start" dominant-baseline="bottom" x="-40" y="-10" dy="0.35em">{tick}%</text>
                <line x1="-40" x2="{innerWidth}" y1="0" y2="0" stroke="hsla(215, 15%, 91%, 1)"></line>
            </g>
          {/if}
        {/each}
          {#each xScale.ticks(10) as tick, i}
              <g class="tick" transform="translate({xScale(tick)}, 0)">
                  <text text-anchor="middle" dominant-baseline="middle" x="0" y={innerHeight + 15}>{formatYear(tick)}</text>
                  <line x1="0" x2="0" y1="{innerHeight}" y2="{innerHeight + 5}" stroke="black"></line>
              </g>
          {/each}
          <Line {xScale} {yScale} {data} {hoveredDate} {innerHeight}/>  
          <HoverEvents bind:hoveredDate {maxDate} {margin} {innerWidth} {innerHeight} {xScale}/>
          <Tooltip {hoveredDate} {xScale} {yScale} {innerWidth} {data}/>
      </g>
    </svg>
  </div>
  <p class="fuente">Fuente: Banco de España</p>
</div>

<style>

  .fuente {
    font-size: 14px;
    font-style: italic;
  }
  
  h1 {
      font-size: 16px;
      margin-bottom: 3px;
      font-weight: 600;
      text-align: left;
  
    }
  
  .subtitulo {
      font-size: 16px;
      margin-bottom: 3px;
      font-weight: 400;
      text-align: left;
  
    }

  /* Estilos para dispositivos con pantallas más grandes (ordenadores) */
  @media (min-width: 768px) {
    .chart-container {
      max-width: 600px;
    }
  }
  
  /* Estilos para dispositivos con pantallas más pequeñas (móviles) */
  @media (max-width: 767px) {
    .chart-container {
      max-width: 100%;
    }
  }
    .tick text {
      font-size: 12px;
      fill: #666;
    }
    line {
      stroke-width: 1px;
    }
  
  </style>