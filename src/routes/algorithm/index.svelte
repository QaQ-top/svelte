<script context="module">
  // your script goes here
</script>

<script>
  import { tick } from "svelte";

  export let segment;
  // 1.Array
  const length = 10;
  const create = (count) => {
    const array = [];
    for (let index = 0; index < count; index++) {
      array.push(Math.ceil(Math.random() * 100));
    }
    return array;
  };
  let array = create(length);

  let watchArray = 0;

  $: {
    array = create(length);
    watchArray;
  }

  const updated = (arr) => {
    array = arr;
  };

  const rking = (array) => {
    let arr = [...array];
    arr.sort((a, b) => {
      updated(arr);
      return a - b;
    });
  };
</script>

<p on:click={() => rking(array)}>重置</p>
<svg width="845" height="300">
  <g>
    {#each array as item, index (index)}
      
      <rect
        x={index * 60}
        y="0"
        width="50"
        height={item * 2}
        fill="#a11"
        class="trin"
      >
      </rect>
      <text x={index * 62} y="280" width='50'>
        {item}
      </text>
    {/each}
  </g>
</svg>

<style>
  .trin {
    transition: all 0.2s linear;
  }
</style>
