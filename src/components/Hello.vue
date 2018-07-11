<template>
  <div class="synth">
    <ul id="example-1">
    <li v-for="note in notes" class="myBox" v-on:click="clickBox(note)">
      {{ note }}
    </li>
  </ul>
  </div>
</template>

<script>
import Tone from 'tone'
export default {
  name: 'VueTone',
  data () {
    let synth = new Tone.MonoSynth().toMaster();
    let notes = ['C4', 'D4', 'E4', 'F4', 'G4', 'A4', 'B4', 'C5'];
    //let arr = Array.apply(null, {length: arrSize}).map(Function.call, Number).map(x => x+1);
    return {
      notes: notes,
      clickBox: function(note) {
        console.log(note);
        synth.triggerAttackRelease(note, '8n')
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
  width: 10%;
}

a {
  color: #35495E;
}
</style>
