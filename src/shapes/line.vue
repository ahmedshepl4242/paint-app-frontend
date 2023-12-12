<template></template>

<script>
export default {
  props: {
    layer: Object,
  },

  methods: {
    createObject(attributes) {
       var line = new Konva.Line({
        points: attributes.points,
        x: attributes.x,
        y: attributes.y,
        stroke:'black',
        strokeWidth: 2,
        id: attributes.id,
        draggable: true,
        rotation: attributes.rotation,
        scaleX: attributes.scaleX,
        scaleY: attributes.scaleY,
        fill: attributes.fill,
        name: 'line'
      });
  
      this.layer.add(line);
    },

    
  },
};
</script>

