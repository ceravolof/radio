<template>
  <v-container>

    <h1> Radio: </h1>


    <v-row>
      <v-col v-for="radio in radios" :key="radio.id" cols="12" sm="6" md="4" lg="3">
        <v-card variant="tonal">
          <v-card-title> <b> {{ radio.name }} </b></v-card-title>
          <v-card-text>
            <img :src="getRadioImage(radio)" alt="Radio Logo" width="100" height="100"> <!-- icon="mdi-radio" -->
          </v-card-text>

          <v-card-actions>
            <v-btn @click="play(radio.url)" class="ms-2" icon="mdi-play" variant="tonal" color="green"></v-btn>
            <v-btn @click="aggPreferito(radio)" class="ms-2" icon="mdi-heart" variant="tonal" color="red"></v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>








    <v-layout class="overflow-visible" style="height: 56px;">
      <v-bottom-navigation v-model="value" color="teal" grow>
        <v-btn @click="playpause()">
          <v-icon id="iconpause">{{ iconName }}</v-icon>
          <b>Pausa</b>
        </v-btn>
      </v-bottom-navigation>
    </v-layout>

  </v-container>
</template>

<script>
import Hls from 'hls.js';

export default {

  name: 'HomeView',
  data() {
    return {
      radios: [],
      iconName: 'mdi-pause',
      isPlaying: false,
      player: null, //da tenere
      preferiti: [],
    }
  },
  methods: {
    getRadios() {
      fetch('https://nl1.api.radio-browser.info/json/stations/search?limit=100&countrycode=IT&hidebroken=true&order=clickcount&reverse=true')
        .then(response => response.json())
        .then(data => {
          this.radios = data;
          console.log(data);
        });

    },

    play(url){
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
 /*
    play(url) {
      if (this.isPlaying) {
        this.player.pause();
      }
      if (typeof url != "undefined") {
        this.player = new Audio(url);
      }
      this.player.play();
      this.isPlaying = true;
    },
*/
    pause() {
      this.player.pause();
      this.isPlaying = false;
      
    },

    aggPreferito(radio) {
      const index = this.preferiti.findIndex(fav => fav.url === radio.url);
      if (index !== -1) {
        this.preferiti.splice(index, 1);
      } else {
        this.preferiti.push(radio);
      }
      localStorage.setItem('preferiti', JSON.stringify(this.preferiti));
    },


    isFavorite(radio) {
      return this.preferiti.some(fav => fav.id === radio.id);
    },
    
    /*
   
    this.currentAudio = new Audio(url);
    this.currentAudio.play();
    */
    playpause() {
      if (this.isPlaying) {
        this.player.pause();
      }//true

    },
    getRadioImage(radio) {
      return radio.favicon || 'https://img.icons8.com/ios-filled/100/radio.png';
    },
  },

  created() {
    this.getRadios();
    const preferiti = localStorage.getItem('preferiti');
    this.preferiti = preferiti ? JSON.parse(preferiti) : [];
  },

};








</script>

<style>
.radio-image {
  max-width: 100%;
  max-height: 100%;
  display: block;
  margin: auto;
}
</style>
