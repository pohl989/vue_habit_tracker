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
        </v-layout>
        <v-layout align-center justify-center>
          <form @submit.prevent="storeHabit">
            <h2>New Habit</h2>
            <v-text-field v-model="title" label="Title" data-vv-name="title" required></v-text-field>
            <v-textarea
              v-model="description"
              label="Description"
              data-vv-name="description"
              required
            ></v-textarea>
            <div class="my-2">
              <v-btn color="error" dark large type="submit">Submit</v-btn>
            </div>
          </form>
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
  apiKey: process.env.VUE_APP_FIREBASE_API_KEY,
  authDomain: process.env.VUE_APP_FIREBASE_AUTH_DOMAIN,
  databaseURL: process.env.VUE_APP_FIREBASE_DATA_URL,
  projectId: process.env.VUE_APP_FIREBASE_PROJECT_ID,
  storageBucket: process.env.VUE_APP_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.VUE_APP_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.VUE_APP_FIREBASE_APP_ID
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