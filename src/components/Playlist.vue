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
    mounted() {
        if (typeof(Storage) !== 'undefined') {
            try {
                let storedPlaylist = JSON.parse(localStorage.getItem('playlist'));

                if (storedPlaylist) {
                    storedPlaylist = storedPlaylist.map((v, i, a) => {
                        return {
                            id: i,
                            name: v.name,
                            isActive: v.isActive,
                            content: localStorage.getItem(`song-${i}`),
                        };
                    });

                    if (!!storedPlaylist) {
                        this.songList.push(...storedPlaylist);
                    }
                }
            }
            catch(err) {
                
            }
        }
    },
    methods: {
        openSongsWindow(e) {
            this.$refs.openSongs.click();
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

            this.saveToLocalStorage(this.songList);

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

                if(reader.result.length < parseInt(5242878 * .9)) {
                    localStorage.setItem(`song-${index}`, reader.result);
                }
                else {
                    const storedPlaylist = Array.from(JSON.parse(
                        localStorage.getItem('playlist')
                    ));

                    try {
                        localStorage.setItem(
                            'playlist', 
                            JSON.stringify(
                                storedPlaylist.filter((v, i, a) => parseInt(i) !== parseInt(index))
                            )
                        );
                    }
                    catch (err) {
                        
                    }
                }
            }
            reader.onerror = err => {
                console.error('Error:', err);
            }
        },

        saveToLocalStorage(songList) {
            if (typeof(Storage) !== 'undefined') {
                localStorage.setItem(
                    'playlist', 
                    JSON.stringify(songList.map(e => {
                        return {
                                id: e.id,
                                isActive: e.isActive,
                                name: e.name
                            };
                        }
                    ))
                );
            }
        },

        playSong(song) {
            this.playSongCallback(song);

            this.songList.map(v => v.isActive = false);
            this.songList[song.id].isActive = true;
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
            <button class="open-songs-button" @click="openSongsWindow">
                <svg class="svg-icon" viewBox="0 0 20 20">
                    <path
                        d="M17.927,5.828h-4.41l-1.929-1.961c-0.078-0.079-0.186-0.125-0.297-0.125H4.159c-0.229,0-0.417,0.188-0.417,0.417v1.669H2.073c-0.229,0-0.417,0.188-0.417,0.417v9.596c0,0.229,0.188,0.417,0.417,0.417h15.854c0.229,0,0.417-0.188,0.417-0.417V6.245C18.344,6.016,18.156,5.828,17.927,5.828 M4.577,4.577h6.539l1.231,1.251h-7.77V4.577z M17.51,15.424H2.491V6.663H17.51V15.424z">
                    </path>
                </svg>
            </button>

            <input name="open-songs-input" id="open-songs-input" type="file" multiple="multiple" v-show="false"
                ref="openSongs" @change="pickSongs" />
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

.open-songs-button {
    padding: 0;
    cursor: pointer;
    height: 29px;
    width: 37px;
    border: none;
    background: none;
    background-color: #585858;
    border-radius: 5px;
}

.open-songs-button:hover,
.open-songs-button:active {
    background-color: #777;
}

.open-songs-button>svg {
    width: 20px;
    height: 30px;
    fill: #fff;
}

.playlist-container {
    height: 408px;
    width: 439px;
    background-color: #3c3c3c;
    position: relative;
    margin-top: 20px;
}

.playlist {
    margin-bottom: 42px;
    overflow: auto;
    max-height: calc(408px - 42px);
    width: 100%;
}
</style>
