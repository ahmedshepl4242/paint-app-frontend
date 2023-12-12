<template></template>

<script>
export default {
  props: {
    layer: Object,
  },

  methods: {
    createObject(attributes) {
      var triang = new Konva.RegularPolygon({
        x: attributes.x,
        y: attributes.y,
        radius: attributes.radius,
        sides: 3,
        stroke:'black',
        strokeWidth: 2,
        id: attributes.id,
        draggable: true,
        rotation: attributes.rotation,
        scaleX: attributes.scaleX,
        scaleY: attributes.scaleY,
        fill: attributes.fill,
        name: 'triangle'
      });

      this.layer.add(triang);
    },
  },
};
</script>
