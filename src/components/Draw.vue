<template>
  <div ref="stage">
    <MyTri ref="tri" :layer="layer"> </MyTri>
    <MyLine ref="line" :layer="layer"> </MyLine>
    <MyEllipse ref="ellip" :layer="layer"> </MyEllipse>
    <MyCircle ref="circ" :layer="layer"> </MyCircle>
    <MyRect ref="rect" :layer="layer"> </MyRect>
    <MySquare ref="square" :layer="layer"> </MySquare>
  </div>
</template>

<script>
import MyRect from "../shapes/rect.vue";
import MyCircle from "../shapes/circ.vue";
import MySquare from "../shapes/square.vue";
import MyEllipse from "../shapes/ellipse.vue";
import MyLine from "../shapes/line.vue";
import MyTri from "../shapes/triangle.vue";
import axios from "axios";

export default {
  components: {
    MyRect,
    MyCircle,
    MySquare,
    MyEllipse,
    MyLine,
    MyTri,
  },
  data() {
    return {
      stage: null,
      layer: null,
      transformer: null,
    };
  },
  props: {
    state: null,
    color: null,
    isOperating: false,
  },
  mounted() {
    this.stage = new Konva.Stage({
      container: this.$refs.stage,
      width: 1330,
      height: 750,
    });

    this.layer = new Konva.Layer();
    this.stage.add(this.layer);

    this.transformer = new Konva.Transformer();
    this.layer.add(this.transformer);

    this.stage.on("click", (evt) => {
      if (evt.target instanceof Konva.Stage) {
        this.transformer.nodes([]);
      } else {
        this.removeHoverMark(evt.target);
      }

      if (this.state == "color") {
        if (evt.target instanceof Konva.Shape) {
          evt.target.fill(this.color);
          this.endOperation();
          this.update(this.createBackendShape(evt.target));
        }
      } else if (this.state == "copy") {
        if (evt.target instanceof Konva.Shape) {
          let target = evt.target;
          let clone = target.clone();
          clone.x(target.x() + 20);
          clone.y(target.y() + 20);
          clone.id(this.generateUUID());

          this.layer.add(clone);
          this.copy(target.id(), clone.id());
          this.endOperation();
        }
      } else if (this.state == "delete") {
        if (evt.target instanceof Konva.Shape) {
          if (
            this.transformer.nodes()[0] &&
            this.transformer.nodes()[0].id() == evt.target.id()
          ) {
            this.transformer.nodes([]);
          }
          evt.target.destroy();
          this.endOperation();
          this.delete(evt.target.id());
        }
      } else if (this.state == null) {
        if (
          !(evt.target instanceof Konva.Stage) &&
          this.transformer.nodes().length == 0
        ) {
          this.transformer.nodes([evt.target]);
        }
      }
    });

    this.stage.on("dragmove", (evt) => {
      let x = evt.target.absolutePosition().x,
        y = evt.target.absolutePosition().y;

      if (x < 0) {
        x = 0;
      } else if (x > this.stage.width() - 15) {
        x = this.stage.width() - 15;
      }

      if (y < 0) {
        y = 0;
      } else if (y > this.stage.height() - 15) {
        y = this.stage.height() - 15;
      }

      evt.target.absolutePosition({ x: x, y: y });
    });

    this.stage.on("mousemove", (evt) => {
      if (this.isOperating && !(evt.target instanceof Konva.Stage)) {
        this.putHoverMark(evt.target);
      }
    });

    this.stage.on("mouseout", (evt) => {
      if (!(evt.target instanceof Konva.Stage)) {
        this.removeHoverMark(evt.target);
      }
    });

    this.stage.on("transformend", (evt) => {
      if (evt.target instanceof Konva.Shape) {
        this.update(this.createBackendShape(evt.target));
      }
    });

    this.stage.on("dragend", (evt) => {
      if (evt.target instanceof Konva.Shape) {
        this.update(this.createBackendShape(evt.target));
      }
    });
  },

  methods: {
    downloadURI(uri, name) {
      var link = document.createElement("a");
      link.download = name;
      link.href = uri;
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
      // delete link;
    },
    saveImage() {
      var dataURL = this.stage.toDataURL();
      this.downloadURI(dataURL, "stage.png");
    },
    generateUUID() {
      return "xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx".replace(
        /[xy]/g,
        function (c) {
          var r = (Math.random() * 16) | 0,
            v = c === "x" ? r : (r & 0x3) | 0x8;
          return v.toString(16);
        }
      );
    },
    addShape(buttonType, attributes) {
      console.log("create");
      console.log(buttonType);

      if (buttonType == "rectangle") {
        this.$refs.rect.createObject(attributes);
      } else if (buttonType == "square") {
        this.$refs.square.createObject(attributes);
      } else if (buttonType == "ellipse") {
        this.$refs.ellip.createObject(attributes);
      } else if (buttonType == "circle") {
        this.$refs.circ.createObject(attributes);
      } else if (buttonType == "line") {
        this.$refs.line.createObject(attributes);
      } else if (buttonType == "triangle") {
        this.$refs.tri.createObject(attributes);
      }
    },
    addNewShape(shapeType) {
      let initials = {
        x: 100,
        y: 100,
        width: 100,
        height: 50,
        id: this.generateUUID(),
        rotation: 0,
        scaleX: 1,
        scaleY: 1,
        fill: null,
        points: [0, 0, 100, 100],
        radiusX: 80,
        radiusY: 50,
        radius: 50,
      };

      this.addShape(shapeType, initials);

      //create shape in backend
      this.create(
        this.createBackendShape(this.layer.findOne("#" + initials.id))
      );
    },
    createBackendShape(shape) {
      let type = shape.name();
      let morefeatures = [];
      switch (type) {
        case "circle":
          morefeatures.push(shape.radius());
          morefeatures.push(0);
          break;
        case "line":
          morefeatures.push(shape.points()[2]);
          morefeatures.push(shape.points()[3]);
          break;
        case "triangle":
          morefeatures.push(shape.radius());
          morefeatures.push(0);

          break;
        case "ellipse":
          morefeatures.push(shape.radiusX());
          morefeatures.push(shape.radiusY());
          break;
        case "rectangle":
          morefeatures.push(shape.height());
          morefeatures.push(shape.width());
          break;
        case "square":
          morefeatures.push(shape.width());
          morefeatures.push(0);

          break;
      }

      const ob = {
        x: shape.x(),
        y: shape.y(),
        rotationangle: shape.rotation(),
        xscale: shape.scaleX(),
        yscale: shape.scaleY(),
        fillcolor: shape.fill(),
        id: shape.id(),
        type: shape.name(),
        morefeatures: morefeatures,
      };

      return ob;
    },
    createAttributes(shape) {
      let width, height, points, radiusX, radiusY, radius;
      switch (shape.type) {
        case "circle":
          radius = shape.morefeatures[0];
          break;
        case "line":
          points = [0, 0, shape.morefeatures[0], shape.morefeatures[1]];
          break;
        case "triangle":
          radius = shape.morefeatures[0];
          break;
        case "ellipse":
          radiusX = shape.morefeatures[0];
          radiusY = shape.morefeatures[1];
          break;
        case "rectangle":
          width = shape.morefeatures[1];
          height = shape.morefeatures[0];
          break;
        case "square":
          width = shape.morefeatures[0];
          break;
      }
      return {
        x: shape.x,
        y: shape.y,
        width: width,
        height: height,
        id: shape.id,
        rotation: shape.rotationangle,
        scaleX: shape.xscale,
        scaleY: shape.yscale,
        fill: shape.fillcolor,
        points: points,
        radiusX: radiusX,
        radiusY: radiusY,
        radius: radius,
      };
    },
    endOperation() {
      this.$emit("endOperation");
    },
    putHoverMark(shape) {
      shape.strokeWidth(4);
    },
    removeHoverMark(shape) {
      shape.strokeWidth(2);
    },

    //takes backend shapes and loads them on the page
    loadShapes(shapes) {
      this.layer.children.forEach((shape) => {
        if (shape instanceof Konva.Shape) {
          shape.destroy();
        }
      });

      for (let shape of shapes) {
        this.addShape(shape.type, this.createAttributes(shape));
      }
      this.transformer.nodes([]);

      this.endOperation();
    },
    save(name, extension) {
      if (extension == "json") {
        this.saveJSN().then((res) => {
          this.downloadFile(res, name, extension);
        });
      } else {
        this.saveXML().then((res) => {
          this.downloadFile(res, name, extension);
        });
      }
    },

    downloadFile(content, name, extension) {
      const blob = new Blob([content], { type: "application/" + extension });

      const link = document.createElement("a");
      link.href = window.URL.createObjectURL(blob);

      link.download = `${name}.${extension}`;

      document.body.appendChild(link);

      link.click();

      document.body.removeChild(link);
    },

    /******************************************************** */
    async refresh() {
      this.layer.destroy();
      this.layer = new Konva.Layer();
      this.stage.add(this.layer);

      console.log("refresh");
      try {
        const response = await axios.delete(
          "https://pa-itb6.onrender.com/api/refresh"
        );
      } catch (e) {
        consol.log(e);
      }
    },
    async delete(id_string) {
      const deleteKey = {
        id: id_string,
      };
      console.log("delete");
      try {
        const response = await axios.delete(
          "https://pa-itb6.onrender.com/api/delete",
          {
            data: deleteKey,
          }
        );

        const res = response.data;

        console.log(res.type);
        console.log(res.shape);
        console.log(res.id);
      } catch (e) {
        console.log(e);
      }
    },

    async undo() {
      console.log("undo");
      try {
        const response = await axios.put("https://pa-itb6.onrender.com/api/undo", {});

        const res = response.data;

        console.log(res.type);
        console.log(res.shape);
        console.log(res.id);

        console.log(res);

        if (res.type == "delete") {
          this.addShape(res.shape.type, this.createAttributes(res.shape)); // need to be updated not correct
        } else if (res.type == "create") {
          this.stage.findOne("#" + res.id).destroy();
        } else if (res.type == "ShapeAttributes") {
          this.stage.findOne("#" + res.id).destroy();
          this.addShape(res.shape.type, this.createAttributes(res.shape));
        }
      } catch (e) {
        console.log(e);
      }
    },
    async redo() {
      console.log("redo");
      try {
        const response = await axios.put("https://pa-itb6.onrender.com/api/redo");

        const res = response.data;

        console.log(res.type);
        console.log(res.shape);
        console.log(res.id);

        if (res.type == "delete") {
          this.addShape(res.shape.type, this.createAttributes(res.shape)); // need to be updated not correct
        } else if (res.type == "create") {
          this.stage.findOne("#" + res.id).destroy();
        } else if (res.type == "ShapeAttributes") {
          this.stage.findOne("#" + res.id).destroy();
          this.addShape(res.shape.type, this.createAttributes(res.shape));
        }
      } catch (e) {
        console.log(e);
      }
    },
    async create(sh) {
      console.log("create");
      console.log(sh);
      try {
        const response = await axios.post(
          "https://pa-itb6.onrender.com/api/create",
          sh
        );

        const res = response.data;

        console.log(res.type);
        console.log(res.shape);
        console.log(res.id);

        console.log(res);
      } catch (e) {
        console.log(e);
      }
    },
    async copy(old_id, new_id) {
      const copyId = {
        oldID: old_id,
        newID: new_id,
      };

      console.log("copy");
      try {
        const response = await axios.post(
          "https://pa-itb6.onrender.com/api/copy",
          copyId
        );
        console.log(response.data);
      } catch (error) {
        console.error(error.response.data);
      }
    },
    async update(shape) {
      console.log("update");
      console.log(shape);
      try {
        const response = await axios.put(
          "https://pa-itb6.onrender.com/api/update",
          shape
        );

        const res = response.data;

        console.log(res.type);
        console.log(res.shape);
        console.log(res.id);

        console.log(res);
      } catch (e) {
        console.log(e);
      }
    },
    async saveXML() {
      try {
        const response = await axios.get("https://pa-itb6.onrender.com/api/savexml");
        const res = response.data;
        console.log(res);

        return res;
      } catch (e) {
        console.log(e);
      }
    },
    async saveJSN() {
      try {
        const response = await axios.get("https://pa-itb6.onrender.com/api/savejson");
        const res = response.data;
        console.log(res);
        return JSON.stringify(res);
      } catch (e) {
        console.log(e);
      }
    },

    async loadJSN(content) {
      console.log(content);

      try {
        const response = await axios.post(
          "https://pa-itb6.onrender.com/api/loadjson",

          JSON.parse(content)
        );
        const res = response.data;
        console.log(res);
        this.loadShapes(res);
      } catch (e) {
        console.log(e);
      }
    },
    async loadXML(content) {
      console.log(content);

      try {
        const response = await axios.post("https://pa-itb6.onrender.com/api/loadxml", {
          xml: content,
        });
        const res = response.data;

        this.loadShapes(res);


        console.log(res);
      } catch (e) {
        console.log(e);
      }
    },
  },
  /***************************** */
};
</script>
