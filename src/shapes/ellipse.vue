<template></template>

<script>
export default {
  props: {
    layer: Object,
  },  

  methods: {
    createObject(attributes) {
      var ellip = new Konva.Ellipse({
        x: attributes.x,
        y: attributes.y,
        radiusX: attributes.radiusX,
        radiusY: attributes.radiusY,
        stroke:'black',
        strokeWidth: 2,
        id: attributes.id,
        draggable: true,
        rotation: attributes.rotation,
        scaleX: attributes.scaleX,
        scaleY: attributes.scaleY,
        fill: attributes.fill,
        name: 'ellipse'
      });

      this.layer.add(ellip);
    },
  },
};
</script>

