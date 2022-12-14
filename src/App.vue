<script>
import { Wave } from '@foobar404/wave';

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
        },
        
        resizeApp() {
            let newDimensionRate = 1;

            if (window.innerWidth / window.innerHeight < this.$refs.container.offsetWidth / this.$refs.container.offsetHeight) {
                newDimensionRate = window.innerWidth / this.$refs.container.offsetWidth;
            }
            else {
                newDimensionRate = window.innerHeight / this.$refs.container.offsetHeight;
            }
            
            this.$refs.container.style.transform = `scale(${newDimensionRate}, ${newDimensionRate})`;
        },
    },
    mounted() {
        let wave = new Wave(
            this.$refs.playerElement.$refs.player,
            this.$refs.barsElement
        );

        wave.addAnimation(
            new wave.animations.Lines({
                    lineWidth: 8,
                    lineColor: 'darkorange',
                    count: 15,
                }
            )
        );
        
        this.resizeApp();
    },
    created() {
        window.addEventListener('keydown', e => {
            if(e.key === ' ' && e.target === document.body) {
                e.preventDefault();
            }
        });

        window.addEventListener('resize', this.resizeApp);
    }
};
</script>

<template>
    <div id="container" ref="container">
        <div class="top">

            <div class="preview">

                <div class="cover">
                    <svg width="191" height="118" viewBox="0 0 191 118" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <rect width="191" height="118" fill="#222222"/>
                        <path d="M120.558 18.0822L76.2505 29.1692C75.0516 29.4693 74.1538 30.5944 74.1538 31.858C74.1538 58.7907 74.1538 54.7663 74.1538 83.0642C71.8365 81.6657 68.9649 80.826 65.8461 80.826C58.2109 80.826 52 85.7993 52 91.913C52 98.0267 58.2109 103 65.8461 103C73.4814 103 79.6923 98.0267 79.6923 91.913V50.6528L118.462 40.9517V66.4339C116.144 65.0352 113.273 64.1953 110.154 64.1953C102.519 64.1953 96.3077 69.1687 96.3077 75.2824C96.3077 81.3962 102.519 86.3694 110.154 86.3694C117.789 86.3694 124 81.3961 124 75.2824C124 56.3549 124 39.7095 124 20.771C124 18.9991 122.321 17.6469 120.558 18.0822Z" fill="white" fill-opacity="0.23"/>
                        <path fill-rule="evenodd" clip-rule="evenodd" d="M153.949 0L3.00552 118H0V0H153.949Z" fill="white" fill-opacity="0.13"/>
                    </svg>

                </div>
            </div>
            <div class="bars">
                <canvas width="154" height="100" ref="barsElement"></canvas>
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
    width: 358px;
    overflow: hidden;
    padding: 27px 0;
    margin-left: auto;
    margin-right: auto;
    margin-top: calc(100vh / 2 - 665px / 2);
    box-sizing: border-box;
}

.top {
    display: flex;
    justify-content: space-between;
    width: 358px;
    margin: 0 0 12px 0;
}

.preview,
.bars {
    background-color: #444;
    width: 170px;
    height: 114px;
    box-sizing: border-box;
}

.preview .cover svg {
    width: 145px;
    padding: 13px;
    height: auto;
}

.bars {
    padding: 0 0 5px 6px;
}

.info {
    display: flex;
    justify-content: space-between;
    font-size: 18px;
    width: 360px;
}

.info .name {
    width: 210px;
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
    width: 138px;
    height: 23px;
    text-align: right;
    padding-right: 14px;
}
</style>