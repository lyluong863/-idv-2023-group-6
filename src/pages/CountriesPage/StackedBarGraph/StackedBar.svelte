<script>
  import { Section } from "@snlab/florence";
  import { scaleLinear } from "d3-scale";
  import { calculateLineLength } from "./calculateLineLength";
  import Bar from "./Bar.svelte";

  export let data; //expect data-container with 3 columns: barName, barLength, mainLabel
  export let viewportWidth;
  export let showing;
  export let linePosition;
  export let textDirection;

  $: enoughBandWidthForLabel = showing === "category" ? 2 : 0.5;
  const y1 = 0;
  const y2 = 0.18;
  const lineHeight = 0.055;
  const total = 24;
  $: distanceGap = viewportWidth < 900 ? 0.18 : 0.16;
  $: fontSize = viewportWidth < 900 ? 10 : 8.5;
  $: barPadding = viewportWidth < 900 ? 1.5 : 1;
  $: yMax = viewportWidth < 900 ? 1.5 : 1.275;

  $: transformed = data
    .cumsum({ xValue: "barLength" }, { asInterval: true })
    .transform((columns) => {
      const xLine = columns.xValue.map(
        (item, i) => item[0] + columns.barLength[i] / 2
      );
      const lineLength = calculateLineLength(
        xLine,
        enoughBandWidthForLabel,
        y1,
        distanceGap
      );
      return { ...columns, lineLength };
    });

  $: scaleX = scaleLinear().domain([0 - barPadding, total + barPadding]);
  $: scaleY = scaleLinear().domain([0, yMax]);
</script>

<Section {scaleX} {scaleY}>
  {#key showing}
    {#each transformed
      .rows()
      .reverse() as { xValue, lineLength, mainLabel, barName, color }}
      <Bar
        x1={xValue[0] + barPadding / 2}
        x2={xValue[1] + barPadding / 2}
        {y1}
        {y2}
        {lineLength}
        {linePosition}
        {textDirection}
        {color}
        {mainLabel}
        subLabel={barName}
        {fontSize}
        strokeWidth={1}
        pointRadius={2}
        {lineHeight}
        {viewportWidth}
      />
    {/each}
  {/key}
</Section>
