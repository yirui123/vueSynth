<template>
<div id="synth">
  <ul id="vertical">
    <li v-for="note in notes" class="myBox1" v-on:mouseover="playNote(note)">
      <!-- {{ note }} -->
    </li>
  </ul>
</div>
</template>

<script>
import Tone from "tone";
import * as Three from 'three';
import OrbitControlModule from "three-orbit-controls";
const OrbitControls = OrbitControlModule( Three );
export default {
  name: "keyboard",
  data() {
    // initialize synth
    let distortion = new Tone.Distortion(Math.random(0, 0.3));
    let tremolo = new Tone.Tremolo().start();
    let polySynth = new Tone.PolySynth(4, Tone.Synth).chain(
      distortion,
      tremolo,
      Tone.Master
    );
    // set keyboard note
    let OriNotes = ["C", "D", "E", "F", "G", "A", "B"];
    let notes = OriNotes.map(x => x + 1);
    for (var i = 2; i < 7; i++) {
      for (var j = 1; j < 8; j++) {
        var newNote = OriNotes[j - 1] + i;
        notes.push(newNote);
      }
    }

    return {
      // set up Threejs env
      camera: null,
      scene: null,
      renderer: null,
      controls: null,
      mesh: null,
      // audio
      notes: notes,
      playNote: function(note) {
        polySynth.triggerAttackRelease(note, "1n");
      }

    };
  },
  methods: {
    init: function() {
      var container = document.getElementById('synth');

      this.camera = new Three.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 10000);
      this.camera.position.set( 0, 0, 100 );

      this.renderer = new Three.WebGLRenderer({
        antialias: true
      });
      this.renderer.setPixelRatio(window.devicePixelRatio);
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      container.appendChild(this.renderer.domElement);

      this.controls = new OrbitControls( this.camera, this.renderer.domElement);
      this.controls.enabled = true;

      this.controls.update();

      this.scene = new Three.Scene();
      this.scene.background = new Three.Color( 0x222222 );

      var rainbowCircleGeometry = new Three.Geometry();
      //We rotate around the circle incrementally, adding vertices outward to one "spoke" at a time.
      for (var d = 0; d <= 360; d = d + 72) {
        var angle = Math.PI * (d / 180);

        //We use the sine and cosine of the angle to arrive at the innermost vertex.
        rainbowCircleGeometry.vertices.push(new Three.Vector3(Math.sin(angle), Math.cos(angle), 0));
        //We don't start building faces until we've completed at least one spoke of the wheel.
        if (rainbowCircleGeometry.vertices.length > 3) {
          //Think about which vertices to add to the face.
          rainbowCircleGeometry.faces.push(new Three.Face3(
            rainbowCircleGeometry.vertices.length - 1,
            rainbowCircleGeometry.vertices.length - 3,
            rainbowCircleGeometry.vertices.length - 4));
          //Here we create vertex colors for each face, one vertex at a time.
          //Think about which colors should go with each vertex to obtain the results you want.
          rainbowCircleGeometry.faces[rainbowCircleGeometry.faces.length - 1].vertexColors[0] = new Three.Color(1, 0, 0);
          rainbowCircleGeometry.faces[rainbowCircleGeometry.faces.length - 1].vertexColors[1] = new Three.Color(0, 1, 0);
          rainbowCircleGeometry.faces[rainbowCircleGeometry.faces.length - 1].vertexColors[2] = new Three.Color(1, 0, 0);
        }

        //This is the next vertex out. Because it's the middle vertex on the spoke, it's
        //part of two different faces.
        rainbowCircleGeometry.vertices.push(new Three.Vector3(Math.sin(angle) * 2, Math.cos(angle) * 2, 0));
        if (rainbowCircleGeometry.vertices.length > 3) {
          rainbowCircleGeometry.faces.push(new Three.Face3(
            rainbowCircleGeometry.vertices.length - 1,
            rainbowCircleGeometry.vertices.length - 4,
            rainbowCircleGeometry.vertices.length - 2));
          rainbowCircleGeometry.faces[rainbowCircleGeometry.faces.length - 1].vertexColors[0] = new Three.Color(0, 1, 0);
          rainbowCircleGeometry.faces[rainbowCircleGeometry.faces.length - 1].vertexColors[1] = new Three.Color(0, 1, 0);
          rainbowCircleGeometry.faces[rainbowCircleGeometry.faces.length - 1].vertexColors[2] = new Three.Color(1, 0, 0);
          rainbowCircleGeometry.faces.push(new Three.Face3(
            rainbowCircleGeometry.vertices.length - 1,
            rainbowCircleGeometry.vertices.length - 3,
            rainbowCircleGeometry.vertices.length - 4));
          rainbowCircleGeometry.faces[rainbowCircleGeometry.faces.length - 1].vertexColors[0] = new Three.Color(0, 1, 0);
          rainbowCircleGeometry.faces[rainbowCircleGeometry.faces.length - 1].vertexColors[1] = new Three.Color(0, 0, 1);
          rainbowCircleGeometry.faces[rainbowCircleGeometry.faces.length - 1].vertexColors[2] = new Three.Color(0, 1, 0);
        }

        //This is the third vertex of the spoke, i.e. the outermost. Like the inner vertex, it is only associated
        //with a single face.
        rainbowCircleGeometry.vertices.push(new Three.Vector3(Math.sin(angle) * 3, Math.cos(angle) * 3, 0));
        if (rainbowCircleGeometry.vertices.length > 3) {
          rainbowCircleGeometry.faces.push(new Three.Face3(
            rainbowCircleGeometry.vertices.length - 1,
            rainbowCircleGeometry.vertices.length - 4,
            rainbowCircleGeometry.vertices.length - 2));
          rainbowCircleGeometry.faces[rainbowCircleGeometry.faces.length - 1].vertexColors[0] = new Three.Color(0, 0, 1);
          rainbowCircleGeometry.faces[rainbowCircleGeometry.faces.length - 1].vertexColors[1] = new Three.Color(0, 0, 1);
          rainbowCircleGeometry.faces[rainbowCircleGeometry.faces.length - 1].vertexColors[2] = new Three.Color(0, 1, 0);
        }
      }

      var rainbowCircleMat1 = new Three.MeshBasicMaterial({
        vertexColors: Three.VertexColors,
        // wireframe: true,
        side: Three.DoubleSide
      });

      this.mesh1 = new Three.Mesh(rainbowCircleGeometry, rainbowCircleMat1);

      var geometry = new Three.CylinderGeometry( 5, 5, 20, 20 );
      var material = new Three.MeshBasicMaterial({vertexColors: Three.VertexColors,side: Three.DoubleSide, wireframe: true});
      this.mesh2 = new Three.Mesh( geometry, material );

      for (var i = 0; i < 5; i++) {
        this.scene.add(this.mesh2);
        this.mesh1.position.set(i*10,0,0);
      }

      this.scene.add(this.mesh1);

    },
    animate: function() {
      requestAnimationFrame(this.animate);
      this.mesh1.rotation.x += 0.01;
      this.mesh1.rotation.y += 0.01;
      this.mesh2.rotation.x += 0.01;
      this.mesh2.rotation.y += 0.01;
      this.mesh2.rotation.z += 0.01;

      this.controls.update();
      this.renderer.render(this.scene, this.camera);
    }
  },
  mounted() {
    this.init();
    this.animate();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
h1,
h2 {
  font-weight: normal;
}

.synth {
  position: absolute;
  left: 0;
  right: 0;
  margin-left: auto;
  margin-right: auto;
  width: 80vw;
}

canvas {
  position: absolute;
  top: 0;
  height: 100vh;
}

ul {
  list-style-type: none;
  padding: 0;
  transform: matrix(1.1, -0.2, 0, 1, 0, 0);
}

.myBox1 {
  width: 5px;
  color: pink;
  background: pink;
  margin-right: calc((80vw - 42*5px)/42);
  cursor: pointer;
  height: 80vh;
  margin-top: 5vh;
  cursor: none;
}

.myBox1:hover {
  background: #000;
  transform: rotate(15deg);
  width: 10px;

}

li {
  display: inline-block;
}

a {
  color: #35495e;
}
</style>
