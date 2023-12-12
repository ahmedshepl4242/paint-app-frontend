<template></template>

<script>
export default {
  props: {
    layer: Object,
  },

  methods: {
    createObject(attributes) {
      var square = new Konva.Rect({
        x: attributes.x,
        y: attributes.y,
        width: attributes.width,
        height: attributes.width,
        stroke:'black',
        strokeWidth: 2,
        id: attributes.id,
        draggable: true,
        rotation: attributes.rotation,
        scaleX: attributes.scaleX,
        scaleY: attributes.scaleY,
        fill: attributes.fill,
        name: 'square'
      });

      this.layer.add(square);
    },
    
  },
};
</script>
