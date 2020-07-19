<template>
  <div id="app" />
</template>

<script lang="ts">
import Vue from "vue";
import Matter from "matter-js";

const {
  Engine,
  Render,
  Runner,
  World,
  Bodies,
  Mouse,
  MouseConstraint
} = Matter;

const SIZES = [20, 32, 44];
const STARTS = [60, 120, 180, 240, 300];

const selectRandom = (array: number[]) => {
  return array[Math.floor(Math.random() * array.length)];
};

const makeCircleRandom = () => {
  const size = selectRandom(SIZES);
  const start = selectRandom(STARTS);
  const circle = Bodies.circle(start, 0, size, {
    restitution: 0.8, // 弾力性
    frictionAir: 0.03 // 空気抵抗
  });
  return circle;
};

const addCircles = (world: Matter.World) => {
  let count = 0;
  const max = 30;

  const add = (world: Matter.World) => {
    if (count === max) return;
    setTimeout(() => {
      World.add(world, makeCircleRandom());
      count++;
      add(world);
    }, 500);
  };

  add(world);
};

const render = () => {
  const engine = Engine.create();
  const world = engine.world;

  /**
   * 描画エリアの設定
   */
  const render = Render.create({
    element: document.body,
    engine: engine,
    options: {
      width: 352,
      height: 512,
      background: "#eee",
      wireframes: false
    }
  });

  Render.run(render);

  const runner = Runner.create();
  Runner.run(runner, engine);

  /**
   * 床と左右の壁を作成
   */
  const floor = Bodies.rectangle(178, 512, 352, 8, {
    isStatic: true
  });

  const wallLeft = Bodies.rectangle(0, 300, 8, 600, {
    isStatic: true
  });

  const wallRight = Bodies.rectangle(352, 300, 8, 600, {
    isStatic: true
  });

  World.add(world, [floor, wallLeft, wallRight]);

  /**
   * マウス操作の設定
   */
  const mouse = Mouse.create(render.canvas);
  const mouseConstraint = MouseConstraint.create(engine, {
    mouse: mouse
  });

  World.add(world, mouseConstraint);

  addCircles(world);
};

export default Vue.extend({
  name: "App",
  created() {
    render();
  }
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
