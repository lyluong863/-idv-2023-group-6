<script>
  import { Graphic, Label } from "@snlab/florence";

  import StackedBar from "./StackedBar.svelte";

  export let subcategoryData;
  export let totalCategoryData;
  export let title;
  export let order; // sort order: 'ascending' | 'descending'
  export let showing; // showing by: 'category' | 'change'
  export let viewportWidth;

  const width = 1000;
  $: height = viewportWidth < 900 ? 240 : 200;

  $: categoriesSorted = totalCategoryData
    .arrange({
      categoryAndHour: order,
    })
    .rename({
      [showing === "category" ? "Category" : "changeLabel"]: "barName",
    });
  $: subcategoriesSorted = subcategoryData
    .arrange({
      categoryAndHour: order,
    })
    .rename({
      [showing === "category" ? "Subcategory" : "changeLabel"]: "barName",
    });
</script>

<Graphic {width} {height} padding={0} flipY>
  <Label
    x={0.05}
    y={0.8}
    fontSize={25}
    fontFamily={"Barlow"}
    fontWeight={"800"}
    text={title}
    anchorPoint={"l"}
  />
  <StackedBar
    linePosition={"down"}
    textDirection={"up"}
    {showing}
    {viewportWidth}
    data={categoriesSorted}
  />
</Graphic>
<Graphic {width} {height} padding={0}>
  <StackedBar
    linePosition={"down"}
    textDirection={"down"}
    {showing}
    {viewportWidth}
    data={subcategoriesSorted}
  />
</Graphic>
