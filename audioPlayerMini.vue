<template>
    <div class="player">
        <slot name="play" :data="playing">
            <a @click.prevent="playing = !playing" title="Play/Pause" href="#">
                <svg width="18px" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20">
                    <path v-if="!playing" fill="currentColor"
                          d="M15,10.001c0,0.299-0.305,0.514-0.305,0.514l-8.561,5.303C5.51,16.227,5,15.924,5,15.149V4.852c0-0.777,0.51-1.078,1.135-0.67l8.561,5.305C14.695,9.487,15,9.702,15,10.001z"/>
                    <path v-else fill="currentColor"
                          d="M15,3h-2c-0.553,0-1,0.048-1,0.6v12.8c0,0.552,0.447,0.6,1,0.6h2c0.553,0,1-0.048,1-0.6V3.6C16,3.048,15.553,3,15,3z M7,3H5C4.447,3,4,3.048,4,3.6v12.8C4,16.952,4.447,17,5,17h2c0.553,0,1-0.048,1-0.6V3.6C8,3.048,7.553,3,7,3z"/>
                </svg>
            </a>
        </slot>
        <audio
            :loop="innerLoop"
            ref="audiofile"
            :src="file"
            preload="auto"
            style="display: none;"
        ></audio>
    </div>
</template>

<script>
  import {empty} from './empty';

  export default {
    name: "audioPlayerMini",
    props: {
      file: String,
      loop: {
        type: Boolean,
        default: false,
      },
      autoPlay: {
        type: Boolean,
        default: false
      },
    },
    data() {
      return {
        innerLoop: false,
        audio: undefined,
        loaded: false,
        playing: false,
      };
    },
    created() {
      this.innerLoop = this.loop;
    },
    mounted() {
      this.audio = this.$el.querySelectorAll('audio')[0];
      this.audio.addEventListener('loadeddata', this.load);
      this.audio.addEventListener('pause', () => {
        if (this.playing)
          this.playing = false;
      });
      this.audio.addEventListener('play', () => {
        this.playing = true;
      });
      this.audio.addEventListener('ended', () => {
        if(!this.innerLoop)
            this.playing = false;
      });
    },
    watch: {
      playing(value) {
        if (value) {
          if (!empty(window.darkPlayer)) {
            window.darkPlayer.stop();
          }
          window.darkPlayer = this.audio;
          return this.audio.play();
        } else {
          window.darkPlayer = false;
        }
        this.audio.pause();
      },
    },
    methods: {
      load() {
        if (this.audio.readyState >= 2) {
          this.loaded = true;
          return this.playing = this.autoPlay;
        }
        throw new Error('Failed to load sound file.');
      },
      stop() {
        this.playing = false;
        this.audio.currentTime = 0;
      },
    },
  }
</script>

<style scoped>

</style>