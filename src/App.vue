<template>
  <div>
  <v-stage ref="stage" :config="stageSize">
    <v-layer ref="layer">
      <v-rect
        ref="rect"
        @dragstart="changeSize"
        @dragend="changeSize"
        :config="{
            width: 50,
            height: 50,
            fill: 'green',
            draggable: true
          }"
      />
      <v-regular-polygon
        ref="hexagon"
        :config="{
          x: 200,
          y: 200,
          sides: 6,
          radius: 20,
          fill: 'red',
          stroke: 'black',
          strokeWidth: 4
        }"
      />
    </v-layer>
  </v-stage>
</div>
<button @click='addWedge'>add wedge</button>
<button @click='save'>save</button>
<button @click='load'>load</button>
</template>

<script>
const width = window.innerWidth;
const height = window.innerHeight;

export default {
  data() {
    return {
      stageSize: {
        width: width,
        height: height
      },
      shapes: [
        {"attrs":{"width":50,"height":50,"fill":"green","draggable":true},"className":"Rect"},
        {"attrs":{"stuff": {"foo":"bar"},"x":785.5272065925454,"y":200,"sides":6,"radius":20,"fill":"red","stroke":"black","strokeWidth":4},"className":"RegularPolygon"},
        {"attrs":{"x":697.5,"y":177.5,"radius":70,"angle":60,"fill":"blue","stroke":"black","strokeWidth":4,"rotation":-120},"className":"Wedge"}
      ]
    };
  },
  methods: {
    changeSize(e) {
      // to() is a method of `Konva.Node` instances
      e.target.to({
        scaleX: Math.random() + 0.8,
        scaleY: Math.random() + 0.8,
        duration: 0.2
      });
    },
    save() {
      let nodes = JSON.parse(this.$refs.stage.getNode().toJSON()).children[0].children;

      this.shapes = [...nodes];

    },
    load() {
      let layer = this.$refs.layer.getNode();
      let nodes;

      layer.destroyChildren();

      this.shapes.forEach(s => {
        let node = new window.Konva[s.className](s.attrs);

        layer.add(node);
      });
    },
    addWedge() {
      var wedge = new window.Konva.Wedge({
        x: this.$refs.stage.getNode().width() / 2,
        y: this.$refs.stage.getNode().height() / 2,
        radius: 70,
        angle: 60,
        fill: 'blue',
        stroke: 'black',
        strokeWidth: 4,
        rotation: -120,
      });

      this.$refs.layer.getNode().add(wedge);
    }
  },
  mounted() {
    const vm = this;
    const amplitude = 100;
    const period = 5000;
    const centerX = vm.$refs.stage.getNode().getWidth() / 2;

    const hexagon = this.$refs.hexagon.getNode();

    const anim = new Konva.Animation(function(frame) {
      hexagon.setX(
        amplitude * Math.sin((frame.time * 2 * Math.PI) / period) + centerX
      );
    }, hexagon.getLayer());

    anim.start();
  }
};
</script>
