<script>
export default {
    name: 'Playlist',
    props: {

    },
    data() {
        return {
            isMounted: false,
            duration: 1,
            currentTime: 0,
        };
    },
    methods: {
        playSong(song) {
            const source = this.$refs.audioSource;
            const player = this.$refs.player;

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
        },

        setTime(e) {
            if (
                e.target.classList.contains('progress-inner') || 
                e.target.classList.contains('previous') 
                
            ) {
                this.$refs.player.currentTime = this.duration * e.offsetX / 434;
            }
        }
    },
    mounted() {
        this.isMounted = true;
    }
};
</script>

<template>
    <div class="player-container">
        <audio controls="controls" id="player" ref="player" @timeupdate="step" v-show="false">
            <source id="audio-source" ref="audioSource" src="" />
        </audio>
    </div>

    <div class="progress-container">
        <div class="progress" @click="setTime">
            <div class="progress-inner">
                <div class="previous" ref="previous" v-bind:style="`width: calc(${currentTime / duration * 100}% + 33px / 2)`"></div>
                <div class="indicator" ref="indicator" v-bind:style="`left: ${currentTime / duration * 100}%`"></div>
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
</style>
