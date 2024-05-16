<template>
    <v-container>
        <h1>Preferiti:</h1>
        <v-row>
            <v-col v-for="pref in preferiti" :key="pref.id" cols="12" sm="6" md="4" lg="3">
                <v-card variant="tonal">
                    <v-card-title> <b> {{ pref.name }} </b></v-card-title>
                    <v-card-text>
                        <img :src="getRadioImage(pref)" alt="Radio Logo" width="100" height="100">
                    </v-card-text>

                    <v-card-actions>
                        <v-btn @click="play(pref.url)" class="ms-2" icon="mdi-play" variant="tonal"
                            color="green"></v-btn>
                        <v-btn @click="menoPreferito(pref)" class="ms-2" icon="mdi-heart" variant="tonal"
                            color="red"></v-btn>
                    </v-card-actions>
                </v-card>
            </v-col>
        </v-row>

        <v-layout class="overflow-visible" style="height: 56px;">
            <v-bottom-navigation v-model="value" color="teal" grow>
                <v-btn @click="playpause()">
                    <v-icon>{{ iconName }}</v-icon>
                    <b>Pausa</b>
                </v-btn>
            </v-bottom-navigation>
        </v-layout>

    </v-container>



</template>
<script>
import Hls from 'hls.js';
export default {
    name: 'ContattiView',
    data() {
        return {
            radios: [],
            iconName: "mdi-pause",
            isPlaying: false,
            player: null, //da tenere
            preferiti: [],
        }
    },
    methods: {
        getRadioImage(radio) {
            return radio.favicon || 'https://img.icons8.com/ios-filled/100/radio.png';
        },
        
        playpause() {
            if (this.isPlaying) {
                this.player.pause();
            }

        },

        play(url) {
            if (this.isPlaying) {
                this.player.pause();

            }
            if (url.includes('m3u8')) {
                if (Hls.isSupported()) {
                    const hls = new Hls();
                    hls.loadSource(url);
                    hls.attachMedia(this.player);
                } else {
                    console.error('il browser non supporta HLS.');
                }
            } else {
                this.player = new Audio(url);
            }
            this.player.play()
                .catch(error => {
                    console.error('Error playing audio:', error);
                    if (error.name === 'NotAllowedError') {
                        console.error('Please ensure that the audio playback is allowed in your browser settings.');
                    }
                });
            this.isPlaying = true;

        },
        pause() {
            this.player.pause();
            this.isPlaying = false;

        },

        menoPreferito(radio) {
            const index = this.preferiti.findIndex(fav => fav.id === radio.id);
            if (index === -1) {
                this.preferiti.push(radio);
            } else {
                this.preferiti.splice(index, 1);
            }
            localStorage.setItem('preferiti', JSON.stringify(this.preferiti));
        },
        getFavoriteRadios() {
            const preferiti = JSON.parse(localStorage.getItem('preferiti')) || [];
            this.preferiti = preferiti;
        },

    },

    created() {
        this.getFavoriteRadios();
    },



}
</script>