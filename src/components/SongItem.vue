<script>
export default {
  name: 'Playlist',
  props: {
    song: Object,
    playSongCallback: Function,
    isActive: Boolean,
  },
  methods: {
    handleItem(song) {
        this.playSongCallback(song);
    }
  }
};
</script>

<template>
    <div :title="song.name" class="song-item" @click="handleItem(song)" :class="{ active: isActive }">
        {{song.name}}
    </div>
</template>

<style scoped>
    .song-item {
        cursor: pointer;
        background-color: #484848;
        color: #fff;
        font-family: sans-serif;
        padding: 13px 14px;
        margin-bottom: 4px;
        font-size: 18px;
        overflow-x: hidden;
    }

    .song-item:hover {
        background-color: #666;
    }

    .active {
        background-color: #D06400;
    }

    .active:hover, .active:active {
        background-color: #e17511;
    }
</style>
