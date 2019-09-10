<template>
  <div id="app">
    <h1>Animating 1000 SVG dots</h1>
    <div id="app">
      <p>
        <button
          @click="toggleOptimization"
        >{{ optimized ? 'disable' : 'enable' }} optimization (Object.freeze)</button>
      </p>
      <svg>
        <circle v-for="point in model.points" :cx="point.x" :cy="point.y" r="2px" fill="#FC309D"></circle>
      </svg>
    </div>
  </div>
</template>

<script>
import Stats from 'stats.js';

const stats = new Stats();
stats.setMode(0);
stats.domElement.style.position = "absolute";
stats.domElement.style.right = "0px";
stats.domElement.style.top = "0px";
document.body.appendChild(stats.domElement);

var WIDTH = 800;
var HEIGHT = 600;

function createModel(count) {
  var points = [];
  for (var i = 0; i < count; ++i) {
    points.push({
      x: Math.random() * WIDTH,
      y: Math.random() * HEIGHT,
      vx: Math.random() * 4 - 2,
      vy: Math.random() * 4 - 2
    });
  }

  return {
    points: points,
    step: step
  };

  function step() {
    points.forEach(move);
  }

  function move(p) {
    if (p.x > WIDTH || p.x < 0) p.vx *= -1;
    if (p.y > HEIGHT || p.y < 0) p.vy *= -1;
    p.y += p.vy;
    p.x += p.vx;
  }
}

export default {
  name: "App",
  data: function () {
    return {
      model: createModel(1000),
      optimized: true
    };
  },
  created: function() {
    var self = this;
    requestAnimationFrame(render);
    stats.begin();
    function render() {
      stats.end();
      stats.begin();
      requestAnimationFrame(render);
      self.model.step();
      if (self.optimized) {
        self.$forceUpdate();
      }
    }
  },
  methods: {
    toggleOptimization: function() {
      this.model = this.optimized
        ? createModel(1000)
        : Object.freeze(createModel(1000));
      this.optimized = !this.optimized;
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
html,
body {
  height: 100%;
  width: 100%;
  padding: 0;
  margin: 0;
}
svg {
  width: 800px;
  height: 600px;
}
</style>
