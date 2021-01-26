<script context="module" lang="ts">
</script>

<script>
  import { tweened } from "svelte/motion";
  import Grid from "../../components/grid/index.svelte";
  import Controls from "../../components/controls/index.svelte";
  import { eases, types } from "./utils";

  export let segment;

  let box;

  const aim = (value) => {
    // console.log(value, box);
    box?.style && (box.style.marginLeft = value + 'px')
  }
  
  let current_type = "In";
  let current_ease = "sine";
  let duration = 1000;
  let current = eases.get(current_ease)[current_type];
  let playing = false;
  let width;

  const ease_path = tweened(current.shape, {
    interpolate: (a, b) => {
      return (t) => {
        return b;
      };
    },
  });
  const x = tweened(0);
  const y = tweened(1000);

  async function runAnimations() {
    playing = true;

    // 恢复 坐标 0 0;
    x.set(0, { duration: 0 });
    y.set(1000, { duration: 0 });


    // 描绘新路径
    await ease_path.set(current.shape);

    // 执行新动画
    await Promise.all([
      x.set(1000, { duration, easing: t => t}),
      y.set(0, { duration, easing: (t) => {
        let go = current.fn(t);
        aim((go * (500 - 50)));
        return go;
      } }),
    ]);

    playing = false;
  }

  $: current = eases.get(current_ease)[current_type];
  $: current && runAnimations();
</script>

<div class='box'>
  <div>
    <div>
      {#each [...eases] as [key, value], index (key)}
        <p on:click={() => (current_ease = key)}>{key}</p>
      {/each}
    </div>
    <div>
      {#each types as [key, value], index}
        <p on:click={() => (current_type = value)}>{key}</p>
      {/each}
    </div>
  </div>
  <div bind:offsetWidth={width} class="easing-vis">
    <svg viewBox="0 0 1400 1802">
      <g class="canvas">
        <Grid x={$x} y={$y} />
        <g class="graph">
          <path stroke="#333" stroke-width="2" fill="none" d={$ease_path} />
          <path
            d="M0,23.647C0,22.41 27.014,0.407 28.496,0.025C29.978,-0.357 69.188,3.744 70.104,4.744C71.02,5.745 71.02,41.499 70.104,42.5C69.188,43.501 29.978,47.601 28.496,47.219C27.014,46.837 0,24.884 0,23.647Z"
            fill="#ff3e00"
            style="transform: translate(1060px, {$y - 24}px)"
          />
          <circle cx={$x} cy={$y} r="15" fill="#ff3e00" />
        </g>
      </g>
    </svg>
  </div>
</div>
<div class='intr' >
  <div bind:this={box} ></div>
</div>

<style>
  .intr {
    width: 500px;
    height: 30px;
    border: 1px solid tomato;
    overflow: hidden;
  }
  .intr > div {
    width: 50px;
    height: 30px;
    background-color: tomato;
  }
  .easing-vis {
    display: flex;
    max-height: 95%;
    max-width: 800px;
    margin: auto;
    padding: 10px;
    border: 1px solid #333;
    border-radius: 2px;
    padding: 20px;
  }

  svg {
    width: 100%;
    margin: 0 20px 0 0;
  }

  .graph {
    transform: translate(200px, 400px);
  }

  @media (max-width: 600px) {
    .easing-vis {
      flex-direction: column;
      max-height: calc(100% - 3rem);
    }
  }
  .box {
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
