<template>
<div>
  <player-settings @setWaveType="setWaveType"></player-settings>
  <v-card @keydown="onKeyDown" @keyup="onKeyUp" tabindex="0" outlined elevation="2" class="player">
    <div class="player-content">
      {{"Click in here to begin playing".toUpperCase()}}
    </div>
  </v-card>
</div>
</template>

<script>
import PlayerSettings from './PlayerSettings';
export default {
  name: 'Player',
  components: {
    PlayerSettings
  },
  props: {
    msg: String
  },
  data() {
    return {
        keyboard: ['KeyX','KeyD','KeyC','KeyF','KeyV','KeyB','KeyH','KeyN','KeyJ','KeyM','KeyK','Comma',
        'KeyR','Digit5','KeyT','Digit6','KeyY','KeyU','Digit8','KeyI','Digit9','KeyO','Digit0','KeyP'],
        drums: ['Slash','Quote','BracketRight'],
        oscillators: [],
        
        audioCtx: new AudioContext(),
        gainNode: null,
        

        //Musical constants (like musical chairs except not)
        halfStep: 1.059484,
        c0: 16.35,
        //Keeping track of the state of the sound generator
        octave: 4,
        keyOffset: 0, // USed for transposing
    }
  },
  methods: {
    onKeyDown(event) {
      var i = this.keyboard.indexOf(event.code);
      
      if(i > -1)
        this.playSound(i);
      else if(event.code == 'KeyQ')
        this.adjustOctave(1);
      else if(event.code == 'KeyA')
        this.adjustOctave(-1);
    },

    onKeyUp(event) {
      var i = this.keyboard.indexOf(event.code);
      
      if(i > -1)
        this.stopSound(i);
    },
    playSound(i) {
      let freq = this.c0 * Math.pow(this.halfStep, this.keyOffset) * Math.pow(2,this.octave) * Math.pow(this.halfStep,i);
      this.oscillators[i].frequency.value = freq;
      this.oscillators[i].connect(this.gainNode);
    },
    stopSound(i) {
      this.oscillators[i].disconnect(this.gainNode);
    },
    adjustOctave(amt) {
      this.octave+=amt;
    },
    stopAllSounds() {
      for(let i = 0; i < this.keyboard.length; i++) {
        this.stopSound(i);
      }
    },
    setWaveType(val) {
       for(let i = 0; i < this.keyboard.length; i++) {
         this.oscillators[i].type = val;
       }
    }

  },
  created() {
    this.gainNode = this.audioCtx.createGain();
    this.gainNode.gain.value = .1;
    this.gainNode.connect(this.audioCtx.destination)
    for(let i = 0; i < this.keyboard.length; i++) {
      this.oscillators.push(this.audioCtx.createOscillator());
      this.oscillators[i].type = "square";
      this.oscillators[i].start();
    }
  }
}
</script>

<style scoped>
  .player-content {
    width: 100%;
    text-align: center;
    font-size: 3rem;
    background-color: gray;
  }

  .player {
    height: 100%;
  }
</style>
