<template>
  <v-app app color="primary">
    <v-content>
      <v-container>
        <Navbar />
        <v-switch
          :value="darkMode"
          @change="toggleDarkMode"
          :label="`toggle ${switchLabel} mode`"
        ></v-switch>
        <!-- Track artis info -->
        <v-card height="60">
          <v-card-title class="primary">
            <h2>{{ getTrackInfo.artist }} - {{ getTrackInfo.title }}</h2>
            <v-spacer></v-spacer>
            <h3>
              {{ getTrackInfo.seek | minutes }}/{{
                getTrackInfo.duration | minutes
              }}
            </h3>
          </v-card-title>
        </v-card>
        <br />
        <br />

        <!-- list playlist -->
        <v-card height="330" :class="{ playlist }">
          <v-list>
            <v-list-tile
              class="d-flex justify-space-between list"
              v-for="(track, index) in playlist"
              :key="track.title"
              v-show="track.display"
              :class="[{ selected: track === selectedTrack }]"
            >
              <v-list-tile-content
                @click="selectTrack(track)"
                @dblclick="play()"
              >
                <v-list-tile-title
                  >{{ index | numbers }} {{ track.artist }} -
                  {{ track.title }}</v-list-tile-title
                >
              </v-list-tile-content>
              <v-spacer></v-spacer>
              {{ track.howl.duration() | minutes }}
            </v-list-tile>
          </v-list>
        </v-card>

        <!-- progress -->
        <v-toolbar flat height="40">
          <v-progress-linear
            class=""
            height="40"
            v-model="trackProgress"
          ></v-progress-linear>
        </v-toolbar>

        <!-- <v-row> -->
        <div>
          <v-toolbar flat height="100" class="">
            <div class="row flex-wrap justify-space-between">
              <span class="volume">
                <v-btn flat icon @click="toggleMute">
                  <template v-if="!this.muted">
                    <v-icon v-if="this.volume >= 0.5">mdi-volume-high</v-icon>
                    <v-icon v-else-if="this.volume > 0"
                      >mdi-volume-medium</v-icon
                    >
                    <v-icon v-else>mdi-volume-low</v-icon>
                  </template>
                  <v-icon v-show="this.muted">mdi-volume-mute</v-icon>
                </v-btn>
                <v-slider
                  v-model="volume"
                  @input="updateVolume(volume)"
                  max="1"
                  step="0.1"
                ></v-slider>
                {{ this.volume * 100 + " %" }}
              </span>
              <v-spacer></v-spacer>
              <span class="controls">
                <v-btn
                  outline
                  fab
                  small
                  color="light-blue"
                  @click="skip('prev')"
                >
                  <v-icon>mdi-skip-previous</v-icon>
                </v-btn>
                <v-btn outline fab small color="light-blue" @click="stop()">
                  <v-icon>mdi-stop</v-icon>
                </v-btn>
                <v-btn
                  outline
                  fab
                  small
                  color="light-blue"
                  @click="play()"
                  v-if="!playing"
                >
                  <v-icon large>mdi-play</v-icon>
                </v-btn>
                <v-btn outline fab small color="red" @click="pause()" v-else>
                  <v-icon>mdi-pause</v-icon>
                </v-btn>
                <v-btn
                  outline
                  fab
                  small
                  color="light-blue"
                  @click="skip('next')"
                >
                  <v-icon>mdi-skip-next</v-icon>
                </v-btn>
                <v-spacer></v-spacer>
                <v-btn flat icon @click="toggleShuffle" v-if="!shuffle">
                  <v-icon color="blue-grey">mdi-shuffle</v-icon>
                </v-btn>
                <v-btn flat icon @click="toggleShufflee" v-else>
                  <v-icon color="light-blue">mdi-shuffle</v-icon>
                </v-btn>
                <v-btn flat icon @click="toggleLoop" v-if="!loop">
                  <v-icon color="blue-grey">mdi-clock</v-icon>
                </v-btn>
                <v-btn flat icon @click="toggleLoopp" v-else>
                  <v-icon color="light-blue">mdi-clock</v-icon>
                </v-btn>
                <br />
                <br />
              </span>
            </div>
          </v-toolbar>
        </div>
        <!-- </v-row> -->
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import Navbar from "@/components/Navbar.vue";
import { Howl, Howler } from "howler";
export default {
  components: { Navbar },
  data() {
    return {
      darkMode: false,
      seek: 0,
      shuffle: false,
      loop: false,
      muted: false,
      volume: 0.5,
      index: 0,
      playing: false,
      selectedTrack: null,
      playlist: [
        {
          title: "Its Only Me",
          artist: "Kaleb J",
          howl: null,
          display: true,
        },
        {
          title: "Runtuh feat Fiersa Besari",
          artist: "Feby Putri",
          howl: null,
          display: true,
        },
        {
          title: "Tenang",
          artist: "Yura Yunita",
          howl: null,
          display: true,
        },
        {
          title: "Im sorry dont leave me",
          artist: "Dylan Matthew",
          howl: null,
          display: true,
        },
        {
          title: "Happier",
          artist: "Olivia Rodrigo",
          howl: null,
          display: true,
        },
        {
          title: "At My Worst",
          artist: "Pink Sweat",
          howl: null,
          display: true,
        },
        {
          title: "Heartbreak Anniversary",
          artist: "Giveo",
          howl: null,
          display: true,
        },
        {
          title: "Demons",
          artist: "Imagine Dragons",
          howl: null,
          display: true,
        },
        {
          title: "Take Me To Church",
          artist: "Hozier",
          howl: null,
          display: true,
        },
        {
          title: "Before You Go",
          artist: "Lewis Capaldi",
          howl: null,
          display: true,
        },
        {
          title: "Snowman",
          artist: "Sia",
          howl: null,
          display: true,
        },
         {
          title: "Unstoppable",
          artist: "Sia",
          howl: null,
          display: true,
        },
         {
          title: "Drivers license x Take me to church",
          artist: "Duo",
          howl: null,
          display: true,
        },
         {
          title: "Monsters",
          artist: "Katie Sky",
          howl: null,
          display: true,
        },
          {
          title: "Night Changes",
          artist: "One Direction",
          howl: null,
          display: true,
        },
          {
          title: "STAY",
          artist: "The Kid LAROI, Justin Bieber",
          howl: null,
          display: true,
        },
         {
          title: "Here's Your Perfect",
          artist: "Jamie Mille",
          howl: null,
          display: true,
        },
         {
          title: "To The Bone",
          artist: "Pamungkas",
          howl: null,
          display: true,
        },
         {
          title: "Beggin you",
          artist: "Måneskin",
          howl: null,
          display: true,
        },
         {
          title: "Imagination",
          artist: "Shawn Mendes",
          howl: null,
          display: true,
        },
        {
          title: "Double take",
          artist: "dhruv",
          howl: null,
          display: true,
        },
      ],
    };
  },
  created: function () {
    this.playlist.forEach((track) => {
      let file = track.title.replace(/\s/g, "_");
      track.howl = new Howl({
        src: [`./playlist/${file}.mp3`],
        onend: () => {
          if (this.loop) {
            this.play(this.index);
          } else {
            this.skip("next");
          }
        },
      });
    });
    Howler.volume(this.volume);
  },
  methods: {
    toggleDarkMode: function () {
      this.$vuetify.theme.dark = !this.$vuetify.theme.dark;
      this.darkMode = !this.darkMode;
    },
    selectTrack(track) {
      this.selectedTrack = track;
    },
    play(index) {
      let selectedTrackIndex = this.playlist.findIndex(
        (track) => track === this.selectedTrack
      );
      if (typeof index === "number") {
        this.index++;
      } else if (this.selectedTrack) {
        if (this.selectedTrack != this.currentTrack) {
          this.stop();
        }
        index = selectedTrackIndex;
      } else {
        index = this.index;
      }
      let track = this.playlist[index].howl;
      if (track.playing()) {
        return;
      } else {
        track.play();
      }

      this.selectedTrack = this.playlist[index];
      this.playing = true;
      this.index = index;
    },
    pause() {
      this.currentTrack.howl.pause();
      this.playing = false;
    },
    stop() {
      this.currentTrack.howl.stop();
      this.playing = false;
    },
    skip(direction) {
      let index = 0,
        lastIndex = this.playlist.length - 1;

      if (this.shuffle) {
        index = Math.round(Math.random() * lastIndex);
        while (index === this.index) {
          index = Math.round(Math.random() * lastIndex);
        }
      } else if (direction === "next") {
        index = this.index + 1;
        if (index >= this.playlist.length) {
          index = 0;
        }
      } else {
        index = this.index - 1;
        if (index < 0) {
          index = this.playlist.length - 1;
        }
      }

      this.skipTo(index);
    },
    skipTo(index) {
      if (this.currentTrack) {
        this.currentTrack.howl.stop();
      }

      this.play(index);
    },
    updateVolume(volume) {
      Howler.volume(volume);
    },
    toggleMute() {
      Howler.mute(!this.muted);
      this.muted = !this.muted;
    },
    toggleLoop(value) {
      this.loop = value;
      this.loop = true;
    },
    toggleLoopp() {
      this.loop = false;
    },
    toggleShuffle(value) {
      this.shuffle = value;
      this.shuffle = true;
    },
    toggleShufflee() {
      this.shuffle = false;
    },
  },
  computed: {
    currentTrack() {
      return this.playlist[this.index];
    },
    progress() {
      if (this.currentTrack.howl.duration() === 0) return 0;
      return this.seek / this.currentTrack.howl.duration();
    },
    trackProgress() {
      return this.progress * 100;
    },
    getTrackInfo() {
      let artist = this.currentTrack.artist,
        title = this.currentTrack.title,
        seek = this.seek,
        duration = this.currentTrack.howl.duration();
      return {
        artist,
        title,
        seek,
        duration,
      };
    },
    switchLabel: function () {
      return this.darkMode ? "light" : "dark";
    },
  },
  watch: {
    playing(playing) {
      this.seek = this.currentTrack.howl.seek();
      let updateSeek;
      if (playing) {
        updateSeek = setInterval(() => {
          this.seek = this.currentTrack.howl.seek();
        }, 250);
      } else {
        clearInterval(updateSeek);
      }
    },
  },
};
</script>
<style scoped>
* {
  font-family: "Nunito", sans-serif !important;
}
.selected {
  background-color: orange;
}
.even {
  background-color: #505050;
}
.playlist {
  overflow: auto;
}
.list {
  cursor: pointer;
}
.volume {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 75vw;
  margin: 0 auto;
}
.controls {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 50vw;
  margin: 0 auto;
}
</style>
