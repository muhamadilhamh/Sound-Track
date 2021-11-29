<template>
  <v-system-bar>
    <v-menu offset-y transition="slide-y-transition">
      <v-btn depressed small right slot="activator">
        <v-icon>mdi-headset</v-icon> MENU
      </v-btn>
      <v-list>
        <v-list-tile @click="dialog = true">
          <v-list-tile-title>About</v-list-tile-title>
        </v-list-tile>
        <v-dialog v-model="dialog" max-width="300">
          <v-card>
            <v-card-title><h2>Sound Track</h2></v-card-title>
            <v-card-text>Version 1.0.0</v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn flat @click="dialog = false">OK</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-list>
    </v-menu>
    <v-spacer></v-spacer>
    Sound Track
    <v-spacer></v-spacer>
    <v-icon>mdi-window-minimize</v-icon>
    <v-icon>mdi-window-maximize</v-icon>
    <v-icon>mdi-window-close</v-icon>
  </v-system-bar>
</template>
 
<script>
  export default {
    data () {
      return {
        dialog: false
      }
    },
  }
</script>