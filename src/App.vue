<script>
import Playlist from './components/Playlist.vue';
import Player from './components/Player.vue';

export default {
  name: 'App',
  data() {
    return {
      currentSong: {
        name: null,
      },
    }
  },
  components: {
    Playlist,
    Player,
  },
  methods: {
    runPlayer: function(song) {
      this.$refs.playerElement.playSong(song);
      
      this.currentSong = song;
    }
  }
};
</script>

<template>
  <div id="container">
    <div class="top">
      
      <div class="preview">

        <div class="cover">
          <svg width="191" height="127" viewBox="0 0 191 127" fill="none" xmlns="http://www.w3.org/2000/svg">
            <rect width="191" height="127" fill="#D9D9D9"/>
            <path fill-rule="evenodd" clip-rule="evenodd" d="M169.198 0L3.28764 127H0V0L169.198 0Z" fill="#E1E1E1"/>
          </svg>
        </div>
      </div>
      <div class="bars">

      </div>
    </div>

    <div class="info">
      <div class="name">
        {{currentSong.name}}
      </div>
      <div class="time">

      </div>
    </div>

    <Player ref="playerElement" />

    <div class="buttons">
      <div class="list-options">

      </div>
      <div class="div-controls">

      </div>
      <div class="volume">

      </div>
    </div>

    <Playlist :playSongCallback="runPlayer" />
  </div>
</template>

<style>
  #container {
    width: 500px;
    padding: 27px;
  }

  .top {
    display: flex;
    justify-content: space-between;
    width: 444px;
    margin: 0 0 12px 0;
  }

  .preview, .bars {
    background-color: #444;
    padding: 10px 8px;
    width: 209px;
    height: 145px;
    box-sizing: border-box;
  }
  .preview .cover svg {
    width: 191px;
    height: 127px;
  }
  
  .name {
    width: 326px;
    height: 23px;
  }
</style>