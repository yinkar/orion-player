<script>
import SongItem from './SongItem.vue';

export default {
    name: 'Playlist',
    components: {
        SongItem,
    },
    props: {
        playSongCallback: Function,
    },
    data() {
        return {
            songList: [],
        };
    },
    
    methods: {
        openSongsWindow(e) {
            this.$refs.openSongs.click();
        },

        clearPlaylist() {
            this.songList.length = 0;
        },

        pickSongs(e) {
            const songs = [];

            Array.from(e.target.files).forEach((v, i, a) => {
                songs.push({
                    name: v.name,
                    file: v,
                    content: null,
                    isActive: false,
                });
            });

            this.songList.push(...songs);

            this.songList.forEach((v, i, a) => {
                this.setContent(v.file, i);
            });

            this.songList.map((v, i, a) => v.id = i);
        },

        setContent(file, index) {
            const reader = new FileReader();
            reader.readAsDataURL(file);
            reader.onload = () => {
                this.songList[index].content = reader.result;
            }
            reader.onerror = err => {
                
            }
        },

        playSong(song) {
            this.playSongCallback(song);

            this.songList.map(v => v.isActive = false);
            this.songList[song.id].isActive = true;
        },

        getSongList() {
            return this.songList;
        }
    }
};
</script>

<template>
    <div class="playlist-container">
        <div class="playlist">
            <div class="playlist-item" v-for="(song, i) in songList">
                <SongItem :song="song" :isActive="song.isActive" :playSongCallback="playSong" :key="i" />
            </div>
        </div>

        <div class="playlist-bottom">
            <button class="open-songs-button playlist-button" @click="openSongsWindow">
                <svg class="svg-icon" viewBox="0 0 20 20">
                    <path
                        d="M17.927,5.828h-4.41l-1.929-1.961c-0.078-0.079-0.186-0.125-0.297-0.125H4.159c-0.229,0-0.417,0.188-0.417,0.417v1.669H2.073c-0.229,0-0.417,0.188-0.417,0.417v9.596c0,0.229,0.188,0.417,0.417,0.417h15.854c0.229,0,0.417-0.188,0.417-0.417V6.245C18.344,6.016,18.156,5.828,17.927,5.828 M4.577,4.577h6.539l1.231,1.251h-7.77V4.577z M17.51,15.424H2.491V6.663H17.51V15.424z">
                    </path>
                </svg>
            </button>

            <input name="open-songs-input" id="open-songs-input" type="file" multiple="multiple" v-show="false"
                ref="openSongs" @change="pickSongs" />

            <button class="clear-playlist-button playlist-button" @click="clearPlaylist">
                <svg width="24" height="24" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 27 25">
                    <path d="M19 14.586l3.586-3.586 1.414 1.414-3.586 3.586 3.586 3.586-1.414 1.414-3.586-3.586-3.586 3.586-1.414-1.414 3.586-3.586-3.586-3.586 1.414-1.414 3.586 3.586zm-7 6.414h-12v-2h12v2zm0-4.024h-12v-2h12v2zm0-3.976h-12v-2h12v2zm12-4h-24v-2h24v2zm0-4h-24v-2h24v2z"/>
                </svg>
            </button>
        </div>

    </div>
</template>

<style scoped>
.playlist-bottom {
    position: absolute;
    bottom: 0;
    left: 0;
    height: 42px;
    width: 100%;
    background-color: #303030;
    padding: 7px;
    box-sizing: border-box;
}

.playlist-button {
    padding: 0;
    cursor: pointer;
    height: 29px;
    width: 37px;
    border: none;
    background: none;
    background-color: #585858;
    border-radius: 5px;
    margin-right: 8px;
    transition: 70ms ease-in-out background-color;
}

.playlist-button:hover,
.playlist-button:active {
    background-color: #777;
}

.playlist-button>svg {
    width: 20px;
    height: 30px;
    fill: #fff;
}

.playlist-container {
    height: 320px;
    width: 360px;
    background-color: #3c3c3c;
    position: relative;
    margin-top: 10px;
    padding-bottom: 42px;
}

.playlist {
    margin-bottom: 42px;
    overflow: auto;
    max-height: calc(360px - 42px);
    width: 100%;
}

.playlist::-webkit-scrollbar-track {
    border-radius: 0.125rem;
    background-color: transparent;
}

.playlist::-webkit-scrollbar {
    border-radius: 0.125rem;
    width: 0.25rem;
}

.playlist::-webkit-scrollbar-thumb {
    border-radius: 0.125rem;
    background-color: gray;
}
</style>
