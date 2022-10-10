<script>
export default {
    name: 'Playlist',
    props: {
        nextSongCallback: Function,
        prevSongCallback: Function,
        updateTimesCallback: Function,
    },
    data() {
        return {
            isMounted: false,
            duration: 1,
            currentTime: 0,
            currentVolume: 0,
            playing: false,
            repeat: false,
            shuffle: false,
        };
    },
    methods: {
        playSong(song) {
            const source = this.$refs.audioSource;
            const player = this.$refs.player;

            source.type = song.content?.split(';').at(0).split(':').at(1);
            source.src = song.content;
            
            player.load();
            player.play();
        },

        step() {
            this.duration = parseFloat(
                !!this.$refs.player.duration ? this.$refs.player.duration : 1
            );
            
            this.currentTime = parseFloat(
                this.$refs.player.currentTime
            );

            this.$refs.previous.style = `left: ${this.currentTime / this.duration * 100}%`;
                
            if (this.$refs.player.paused) {
                this.playing = false;
            }
            else {
                this.playing = true;
            }

            this.currentVolume = this.$refs.player.volume;

            this.updateTimesCallback(this.currentTime, this.duration);
        },

        setTime(e) {
            if (
                [
                    'progress', 
                    'previous'
                ].some(v => e.target.classList.contains(v))
            ) {
                this.$refs.player.currentTime = Math.max(this.duration * e.offsetX, 434) / 434;
            }
        },

        setVolume(e) {
            if (
                [
                    'volume-progress', 
                    'volume-previous'
                ].some(v => e.target.classList.contains(v))
            ) {
                this.$refs.player.volume = e.offsetX / 133;
            }

            this.currentVolume = this.$refs.player.volume;
        },

        playPause() {
            if (this.$refs.player.readyState === 0) return;

            if (this.$refs.player.paused) {
                this.$refs.player.play();
                this.playing = true;
            }
            else {
                this.$refs.player.pause();
                this.playing = false;
            }
        },

        songFinished() {
            if (!this.repeat) {
                if (!this.shuffle) {                
                    this.nextSongCallback();
                }
                else {                
                    this.nextSongCallback(Math.floor(Math.random() * 100) + 25);
                }
            }
            else {
                this.$refs.player.play();
                this.playing = true;
            }
        },

        toggleRepeat() {
            this.repeat = !this.repeat;
        },

        toggleShuffle() {
            this.shuffle = !this.shuffle;
        },

        nextSong() {
            if (this.shuffle) {
                this.nextSongCallback(Math.floor(Math.random() * 100) + 25);
            }
            else {            
                this.nextSongCallback();
            }
        },

        prevSong() {
            if (this.shuffle) {
                this.prevSongCallback(Math.floor(Math.random() * 100) + 25);
            }
            else {            
                this.prevSongCallback();
            }
        },
    },
    mounted() {
        this.isMounted = true;

        window.addEventListener('keyup', e => {
            if (e.key === ' ') {
                this.playPause();
            }
        });
    }
};
</script>

<template>
    <div class="player-container">
        <audio controls="controls" id="player" ref="player" @timeupdate="step" @ended="songFinished" v-show="false">
            <source id="audio-source" ref="audioSource" src="" />
        </audio>
    </div>

    <div class="progress" @click="setTime">
        <div class="progress-inner">
            <div class="previous" ref="previous" v-bind:style="`width: calc(${currentTime / duration * 100}% + 33px / 2)`"></div>
            <div class="indicator" ref="indicator" v-bind:style="`left: ${currentTime / duration * 100}%`"></div>
        </div>
    </div>

    <div class="panel">
        <div class="list-options">
            <button class="shuffle-button" @click="toggleShuffle" :class="{ active: shuffle }">
                <svg width="24" height="28" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <g clip-path="url(#clip0_5_43)">
                        <path
                            d="M10.59 9.17L5.41 4L4 5.41L9.17 10.58L10.59 9.17ZM14.5 4L16.54 6.04L4 18.59L5.41 20L17.96 7.46L20 9.5V4H14.5ZM14.83 13.41L13.42 14.82L16.55 17.95L14.5 20H20V14.5L17.96 16.54L14.83 13.41V13.41Z"
                            fill="white" />
                    </g>
                    <defs>
                        <clipPath id="clip0_5_43">
                            <rect width="24" height="24" fill="white" />
                        </clipPath>
                    </defs>
                </svg>

            </button>

            <button class="repeat-button" @click="toggleRepeat" :class="{ active: repeat }">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <path
                        d="M11.1573 10.5668H4.4045C4.18111 10.5668 4 10.3856 4 10.162V3.40476C4 3.18123 4.18111 3 4.4045 3H6.02248C6.24586 3 6.42697 3.18123 6.42697 3.40476V6.03949C7.96988 4.32489 10.211 3.25196 12.7025 3.27008C17.3174 3.30361 21.0098 7.03514 21 11.6531C20.9902 16.2647 17.2513 20 12.6405 20C10.4801 20 8.51137 19.1799 7.02775 17.834C6.85587 17.6781 6.84795 17.4106 7.012 17.2464L8.15696 16.1007C8.30777 15.9498 8.55006 15.9416 8.7098 16.083C9.75596 17.0093 11.1319 17.5714 12.6405 17.5714C15.9192 17.5714 18.573 14.9163 18.573 11.6349C18.573 8.35409 15.9197 5.69841 12.6405 5.69841C10.6687 5.69841 8.92314 6.65891 7.84469 8.13822H11.1573C11.3807 8.13822 11.5618 8.31945 11.5618 8.54298V10.162C11.5618 10.3856 11.3807 10.5668 11.1573 10.5668V10.5668Z"
                        fill="white" />
                </svg>

            </button>
        </div>
        <div class="player-controls">
            <button class="song-navigation prev-song" @click="prevSong">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <g clip-path="url(#clip0_6_60)">
                        <path d="M6 6H8V18H6V6ZM9.5 12L18 18V6L9.5 12Z" fill="white" />
                    </g>
                    <defs>
                        <clipPath id="clip0_6_60">
                            <rect width="24" height="24" fill="white" />
                        </clipPath>
                    </defs>
                </svg>

            </button>

            <button class="play-button" @click="playPause">
                <svg width="44" height="44" viewBox="0 0 44 44" fill="none" xmlns="http://www.w3.org/2000/svg" v-if="!playing">
                    <g clip-path="url(#clip0_6_55)">
                        <path d="M14.6667 9.16666V34.8333L34.8333 22L14.6667 9.16666Z" fill="white" />
                    </g>
                    <defs>
                        <clipPath id="clip0_6_55">
                            <rect width="44" height="44" fill="white" />
                        </clipPath>
                    </defs>
                </svg>

                <svg width="24" height="24" viewBox="0 0 25 23" fill="none" xmlns="http://www.w3.org/2000/svg" v-if="playing">
                    <path d="M8 18H11V5H8V18ZM14 5V18H17V5H14Z" fill="white"/>
                </svg>
            </button>

            <button class="song-navigation next-song" @click="nextSong">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <g clip-path="url(#clip0_6_69)">
                        <path d="M6 18L14.5 12L6 6V18ZM16 6V18H18V6H16Z" fill="white" />
                    </g>
                    <defs>
                        <clipPath id="clip0_6_69">
                            <rect width="24" height="24" fill="white" />
                        </clipPath>
                    </defs>
                </svg>

            </button>
        </div>
        <div class="volume">
            <div class="volume-progress" @click="setVolume">
                <div class="volume-progress-inner">
                    <div class="volume-previous" ref="volume-previous"
                        v-bind:style="`width: calc(${currentVolume * 100}% + 13px / 2)`"></div>
                    <div class="volume-indicator" ref="volume-indicator"
                        v-bind:style="`left: ${currentVolume * 100}%`"></div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.progress,
.progress .previous {
    height: 21px;
    border-radius: 58px;
}

.progress {
    width: 434px;
    background-color: #5e5e5e;
    position: relative;
    margin: 10px 0;
    cursor: pointer;
}

.progress > .progress-inner {
    position: absolute;
    left: 0;
    top: 0;
    width: calc(434px - 33px);
    height: 100%;
    pointer-events: none;
}

.progress .previous {
    background-color: #ed8000;
    width: 0;
    position: absolute;
    left: 0;
    top: 0;
    height: 100%;
    transition: 350ms ease width;
}

.progress .indicator {
    width: 33px;
    height: 33px;
    background-color: #ababab;
    position: absolute;
    top: calc(-33px / 2 + 21px / 2);
    border-radius: 50%;
    left: 0;
    transition: 350ms ease left;
}

.panel {
    display: flex;
    width: 434px;
    justify-content: space-between;
    align-items: center;
    margin: 10px 0 10px 0;
}

.volume-progress,
.volume-progress .volume-previous {
    height: 9px;
    border-radius: 58px;
}

.volume-progress {
    width: 133px;
    background-color: #5e5e5e;
    position: relative;
    margin: 10px 0;
    cursor: pointer;
}

.volume-progress>.volume-progress-inner {
    position: absolute;
    left: 0;
    top: 0;
    width: calc(133px - 13px);
    height: 100%;
    pointer-events: none;
}

.volume-progress .volume-previous {
    background-color: #ed8000;
    width: 0;
    position: absolute;
    left: 0;
    top: 0;
    height: 100%;
    transition: 350ms ease width;
}

.volume-progress .volume-indicator {
    width: 13px;
    height: 13px;
    background-color: #ababab;
    position: absolute;
    top: calc(-13px / 2 + 9px / 2);
    border-radius: 50%;
    left: 0;
    transition: 350ms ease left;
}

.panel button {
    padding: 0;
    cursor: pointer;
    height: 29px;
    width: 37px;
    border: none;
    background: none;
    background-color: #585858;
    border-radius: 5px;
    padding-top: 2px;
    transition: 70ms ease-in-out background-color;
}

.panel button:hover,
.panel button:active {
    background-color: #777;
}

.panel svg {
    width: 20px;
    height: 26px;
    fill: #fff;
}

.panel>div {
    width: 133px;
}

.player-controls {
    display: flex;
    justify-content: space-around;
    align-items: center;
}

.player-controls button {
    border-radius: 50%;
    width: 30px;
    height: 30px;
}

.player-controls button.play-button {
    width: 50px;
    height: 50px;
}

.player-controls svg {
    width: 20px;
    height: 26px;
}

.list-options button {
    margin-right: 12px;
}

.play-button svg {
    width: 35px;
    height: 45px;
}

.active {
    background-color: #D06400 !important;
}

.active:hover, .active:active {
    background-color: #e17511 !important;
}
</style>
