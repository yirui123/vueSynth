<template>
  <div class="synth">
    <ul id="vertical">
      <li v-for="note in notes" class="myBox1" v-on:mouseover="playNote(note)">
        <!-- {{ note }} -->
      </li>
    </ul>

  </div>
</template>

<script>
import Tone from "tone";
export default {
  name: "VueTone",
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
    for (var i = 2; i < 9; i++) {
      for (var j = 1; j < 8; j++) {
        var newNote = OriNotes[j - 1] + i;
        notes.push(newNote);
      }
    }

    return {
      notes: notes,
      playNote: function(note) {
        polySynth.triggerAttackRelease(note, "4n");
      }
    };
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
  width: 60vw;
}

ul {
  list-style-type: none;
  padding: 0;
}
ul:hover {
  transform: matrix(1, -0.3, 0, 1, 0, 0);
}

.myBox1 {
  width: calc(60vw / 112);
  background: pink;
  margin-right: calc(60vw / 112);
  cursor: pointer;
  height: 80vh;
  margin-top: 5vh;
  cursor: none;
}

.myBox1:hover {
  background: #000;
  cursor: url("../assets/cat.png"), auto;
  transform: rotate(5deg);
}

li {
  display: inline-block;
}

a {
  color: #35495e;
}
</style>
