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
            isMounted: false,
            currentTime: '00:00',
            duration: '00:00',
        }
    },
    components: {
        Playlist,
        Player,
    },
    methods: {
        runPlayer(song) {
            this.$refs.playerElement.playSong(song);

            this.currentSong = song;
        },

        nextSong(randomNumber = 1) {
            const playlist = this.$refs.playlistElement.getSongList();

            this.$refs.playlistElement.playSong(
                playlist.at((this.currentSong.id + randomNumber) % playlist.length)
            );
        },

        prevSong() {
            const playlist = this.$refs.playlistElement.getSongList();

            this.$refs.playlistElement.playSong(
                playlist.at(this.currentSong.id - 1)
            );
        },

        updateTimes(currentTime, duration) {
            const format = seconds => {
                const date = new Date(null);
                date.setSeconds(seconds);

                return date.toISOString().substring(14, 19);
            };

            this.currentTime = format(currentTime);
            this.duration = format(duration);
        }
    },
};
</script>

<template>
    <div id="container">
        <div class="top">

            <div class="preview">

                <div class="cover">
                    <svg width="191" height="127" viewBox="0 0 191 127" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <rect width="191" height="127" fill="#D9D9D9" />
                        <path fill-rule="evenodd" clip-rule="evenodd" d="M169.198 0L3.28764 127H0V0L169.198 0Z"
                            fill="#E1E1E1" />
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
                {{currentTime}} - {{duration}}
            </div>
        </div>

        <Player ref="playerElement" :nextSongCallback="nextSong" :prevSongCallback="prevSong" :updateTimesCallback="updateTimes" />

        <Playlist ref="playlistElement" :playSongCallback="runPlayer" />
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

.preview,
.bars {
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

.info {
    display: flex;
    justify-content: space-between;
    font-size: 18px;
    width: 454px;
}

.info .name {
    width: 200px;
    height: 23px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    animation: marquee 5s ease-in-out alternate infinite; 
}

@keyframes marquee {
    0% {
        text-indent: 50%;
    }
    100% {
        text-indent: -100%;
    }
}

.info .time {
    width: 180px;
    height: 23px;
    text-align: right;
    padding-right: 14px;
}
</style>