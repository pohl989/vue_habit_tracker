<template>
  <v-app id="inspire">
    <v-app-bar app clipped-left>
      <v-toolbar-title>Habit Tracker</v-toolbar-title>
    </v-app-bar>

    <v-content>
      <v-container class="pa-2" fluid grid-list-mdt>
        <v-layout column>
          <v-flex>
            <v-card v-if="!editingHabit" color="purple lighten-4">
              <v-list-item three-line>
                <form @submit.prevent="storeHabit">
                  <h2>New Habit</h2>
                  <v-text-field v-model="title" label="Title" data-vv-name="title" required></v-text-field>
                  <v-text-field
                    v-model="description"
                    label="Description"
                    data-vv-name="description"
                    required
                  ></v-text-field>
                  <div class="my-2">
                    <v-btn color="error" dark large type="submit">Submit</v-btn>
                  </div>
                </form>
              </v-list-item>
            </v-card>
          </v-flex>
          <v-flex v-for="habit in habits" v-bind:key="habit.id">
            <div style="margin: 2px">
              <v-card dark tile>
                <v-list-item one-line>
                  <v-list-item-content v-if="habit !== editingHabit" class="align-self-start">
                    <v-row>
                      <v-col cols="12" sm="1" md="1">
                        <v-btn text icon color="green">
                          <v-icon>mdi-check-bold</v-icon>
                        </v-btn>
                        <v-btn text icon color="deep-purple">
                          <v-icon>mdi-check-bold</v-icon>
                        </v-btn>
                        <v-btn text icon color="green">
                          <v-icon>mdi-check-bold</v-icon>
                        </v-btn>
                        <v-btn text icon color="deep-purple">
                          <v-icon>mdi-check-bold</v-icon>
                        </v-btn>
                      </v-col>
                    </v-row>
                    <v-list-item-title class="headline" v-text="habit.title"></v-list-item-title>
                    <p>{{habit.description}}</p>
                    <a @click.prevent="deleteHabit(habit)" href="#" class="card-link">Delete</a>
                    <a @click.prevent="editHabit(habit)" href="#" class="card-link">Edit</a>
                  </v-list-item-content>
                  <v-list-item-content v-else class="align-self-start">
                    <form @submit.prevent="storeHabit">
                      <h2>Edit Habit</h2>
                      <v-text-field v-model="title" label="Title" data-vv-name="title" required></v-text-field>
                      <v-text-field
                        v-model="description"
                        label="Description"
                        data-vv-name="description"
                        required
                      ></v-text-field>
                      <div class="my-2">
                        <a @click.prevent="cancelEditing" href="#" class="card-link">Cancel</a>
                        <a @click.prevent="updateHabit" href="#" class="card-link">Update</a>
                        <!-- <v-btn color="error" dark large type="Save">Submit</v-btn> -->
                      </div>
                    </form>
                  </v-list-item-content>
                </v-list-item>
              </v-card>
            </div>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>

    <v-bottom-navigation dark shift fixed>
      <v-btn>
        <span>Video</span>
        <v-icon>mdi-television-play</v-icon>
      </v-btn>
      <v-btn>
        <span>Book</span>
        <v-icon>mdi-book</v-icon>
      </v-btn>
      <v-btn>
        <span>Image</span>
        <v-icon>mdi-image</v-icon>
      </v-btn>
    </v-bottom-navigation>
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
const habitsRef = database.ref("habits");
export default {
  props: {
    source: String
  },
  data: () => ({
    drawer: null,
    title: "",
    description: "",
    habits: [],
    editingHabit: null
  }),
  methods: {
    storeHabit() {
      habitsRef.push({ title: this.title, description: this.description });
      this.title = "";
      this.description = "";
    },
    deleteHabit(habit) {
      habitsRef.child(habit.id).remove();
    },
    editHabit(habit) {
      this.editingHabit = habit;
      this.title = habit.title;
      this.description = habit.description;
    },
    updateHabit() {
      habitsRef
        .child(this.editingHabit.id)
        .update({ title: this.title, description: this.description });
      this.cancelEditing();
    },
    cancelEditing() {
      this.editingHabit = null;
      this.title = null;
      this.description = null;
    }
  },
  created() {
    this.$vuetify.theme.dark = true;
    habitsRef.on("child_added", snapshot =>
      this.habits.push({ ...snapshot.val(), id: snapshot.key })
    );
    habitsRef.on("child_removed", snapshot => {
      const deletedHabit = this.habits.find(habit => habit.id === snapshot.key);
      const index = this.habits.indexOf(deletedHabit);
      this.habits.splice(index, 1);
    });
    habitsRef.on("child_changed", snapshot => {
      const updatedHabit = this.habits.find(habit => habit.id === snapshot.key);
      updatedHabit.title = snapshot.val().title;
      updatedHabit.description = snapshot.val().description;
    });
  }
};
</script>