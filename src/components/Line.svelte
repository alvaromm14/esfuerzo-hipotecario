<script>
    export let data;
    export let xScale;
    export let yScale;
    export let hoveredDate;
    export let innerHeight;
    import * as d3 from 'd3';

    $: dataBeforeHover = data.filter((d) => d.Fecha <= hoveredDate);

    import { line } from "d3-shape";

    $: lineGenerator = line()
        .curve(d3.curveBasis)
        .x((d) => xScale(d.Fecha))
        .y((d) => yScale(d.Asequibilidad));


    $: areaAsequibilidad = d3.area()
        .curve(d3.curveBasis)
        .x(d => xScale(d.Fecha))
        .y0(innerHeight)
        .y1(d => yScale(d.Asequibilidad));

    $: areaAsequibilidad = d3.area()
        .curve(d3.curveBasis)
        .x(d => xScale(d.Fecha))
        .y0(innerHeight)
        .y1(d => yScale(35));

</script>

<line
    x1={xScale(data[0]?.Fecha)}
    x2={xScale(data[data.length - 1]?.Fecha)}
    y1={yScale(35)}
    y2={yScale(35)}
    stroke="orange"
    fill="transparent"
    stroke-width="0.7"
/>

<path
    d={areaAsequibilidad(data)}
    fill="orange"
    fill-opacity="0.1"
/>

<text
    x={innerWidth > 600 ? (xScale(data[0]?.Fecha) + xScale(data[data.length - 1]?.Fecha)) / 10 : (xScale(data[0]?.Fecha) + xScale(data[data.length - 1]?.Fecha)) / 2}
    y={yScale(35) + 80}
    fill="black"
    font-size="12"
    font-style="italic"
    text-anchor="middle"
    stroke="white"
    stroke-width="3"
    paint-order="stroke"
>
    {"Umbral del 35%"}
</text>

<path
    d={lineGenerator(dataBeforeHover)}
    stroke="steelblue"
    fill="transparent"
    stroke-width="2"
/>

<path
    d={lineGenerator(data)}
    stroke="steelblue"
    fill="transparent"
    stroke-width="2"
    opacity=".35"
/>