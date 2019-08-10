<template>
  <v-app id="inspire">
    <v-navigation-drawer v-model="drawer" app clipped>
      <v-list dense>
        <v-list-item @click>
          <v-list-item-action>
            <v-icon>dashboard</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Dashboard</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item @click>
          <v-list-item-action>
            <v-icon>settings</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Settings</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar app clipped-left>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Habit Tracker</v-toolbar-title>
    </v-app-bar>

    <v-content>
      <v-container fluid fill-height>
        <v-layout align-center justify-center>
          <ul id="example-1">
            <li v-for="habit in habits" v-bind:key="habit.id">
              <h2>{{ habit.title }}</h2>
              <p>{{habit.description}}</p>
            </li>
          </ul>
          <hr />
          <!-- New Message -->
          <form @submit.prevent="storeHabit">
            <h2>New Habit</h2>
            <div class="form-group">
              <label>Description:</label>
              <textarea v-model="description" class="form-control"></textarea>
            </div>
            <div class="form-group">
              <label>Title:</label>
              <input v-model="title" class="form-control" />
            </div>
            <button class="btn btn-primary">Submit</button>
          </form>

          <!-- <v-flex shrink>
            <v-tooltip right>
              <template v-slot:activator="{ on }">
                <v-btn :href="source" icon large target="_blank" v-on="on">
                  <v-icon large>mdi-code-tags</v-icon>
                </v-btn>
              </template>
              <span>Source</span>
            </v-tooltip>
            <v-tooltip right>
              <template v-slot:activator="{ on }">
                <v-btn
                  icon
                  large
                  href="https://codepen.io/johnjleider/pen/bXNzZL"
                  target="_blank"
                  v-on="on"
                >
                  <v-icon large>mdi-codepen</v-icon>
                </v-btn>
              </template>
              <span>Codepen</span>
            </v-tooltip>
          </v-flex>-->
        </v-layout>
      </v-container>
    </v-content>

    <v-footer app>
      <span>&copy; 2019</span>
    </v-footer>
  </v-app>
</template>

<script>
import firebase from "firebase";

const firebaseConfig = {
  apiKey: "AIzaSyA68bpiiQ_tZT9TJG2na0HkKZjtvKz1tGc",
  authDomain: "chico-habit-tracker.firebaseapp.com",
  databaseURL: "https://chico-habit-tracker.firebaseio.com",
  projectId: "chico-habit-tracker",
  storageBucket: "",
  messagingSenderId: "774533492669",
  appId: "1:774533492669:web:d5d2266f88fb95b8"
};

const myFire = firebase.initializeApp(firebaseConfig);
const database = myFire.database();

export default {
  props: {
    source: String
  },
  data: () => ({
    drawer: null,
    title: "",
    description: "",
    habits: []
  }),
  methods: {
    storeHabit() {
      database
        .ref("habits")
        .push({ title: this.title, description: this.description });
      this.title = "";
      this.description = "";
    }
  },
  created() {
    this.$vuetify.theme.dark = true;
    database
      .ref("habits")
      .on("child_added", snapshot => this.habits.push(snapshot.val()));
  }
};
</script>