<template>
  <div>
    <v-app-bar :elevation="20" absolute shrink-on-scroll>
      <header>
        <h1>{{ title }}</h1>
      </header>
      <v-spacer></v-spacer>
      <div class="button-containter">
        <v-btn
          class="login-btn"
          v-if="showLoginButton"
          v-on:click="showLoginDialog = true"
          >Log in</v-btn
        >
        <v-btn
          color="#000000b8"
          dark
          class="signup-btn"
          v-if="showSignupButton"
          v-on:click="showSignupDialog = true"
          >Sign up</v-btn
        >
        <v-btn class="login-btn" v-if="showLogoutButton" v-on:click="logoutUser"
          >Log out</v-btn
        >
      </div>
    </v-app-bar>

    <v-dialog v-model="showLoginDialog" max-width="400px">
      <v-card>
        <v-card-title>
          <span>Login</span>
          <v-btn icon @click="showLoginDialog = false">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-card-title>
        <v-card-text>
          <p>Enter your email and password to log in:</p>
          <v-text-field v-model="enterEmail" label="Email"></v-text-field>
          <v-text-field
            v-model="enterPassword"
            label="Password"
            type="password"
          ></v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="addLogin()">Log in</v-btn>
          <v-btn @click="showLoginDialog = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar v-model="showSnackbar" :timeout="3000" top>{{
      snackbarMessage
    }}</v-snackbar>

    <v-dialog v-model="showSignupDialog" max-width="400px">
      <v-card>
        <v-card-title>
          <span>Sign up</span>
          <v-btn icon @click="showSignupDialog = false">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-card-title>
        <v-card-text>
          <p>Enter your email and password to sign up:</p>
          <v-text-field v-model="newEmail" label="Email"></v-text-field>
          <v-text-field
            v-model="newPassword"
            label="Password"
            type="password"
          ></v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="addUser()">Sign up</v-btn>
          <v-btn @click="showSignupDialog = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar v-model="showSnackbar" :timeout="3000" top>{{
      snackbarMessage
    }}</v-snackbar>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import axios from "axios";

interface User {
  email: string;
  password: string;
}

@Component
export default class MyComponent extends Vue {
  users: User[] = [];
  showSignup = false;
  showLogin = false;
  showLoginButton = true;
  showSignupButton = true;
  showLogoutButton = false;
  title = "ReciPy";

  newEmail = "";
  newPassword = "";
  enterEmail = "";
  enterPassword = "";
  dialog: any;
  showLoginDialog = false;
  showSignupDialog = false;
  showSnackbar = false;
  snackbarMessage = "";

  async addUser() {
    const usr: User = {
      email: this.newEmail,
      password: this.newPassword,
    };

    try {
      await axios.post("http://localhost:3000/signup/", usr, {
        withCredentials: true,
      });
      // Reset newEmail and newPassword fields to clear input
      this.newEmail = "";
      this.newPassword = "";
      // Close signup dialog
      this.showSignupDialog = false;
    } catch (error) {
      console.log(error);
      this.snackbarMessage = "Failed to sign up. Please try again.";
      this.showSnackbar = true;
    }
  }

  async addLogin() {
    const usr: User = {
      email: this.enterEmail,
      password: this.enterPassword,
    };

    axios
      .post("http://localhost:3000/login", usr, {
        withCredentials: true,
      })
      .then((response: { data: { token: any } }) => {
        const token = response.data.token;
        // Save token to local storage
        localStorage.setItem("token", token);
        this.showLoginDialog = false;
        if (localStorage.getItem("token")) {
          this.showLoginButton = false;
          this.showSignupButton = false;
          this.showLogoutButton = true;
        }
      })
      .catch((error: any) => {
        console.log(error);
        this.snackbarMessage =
          "Failed to log in. Please check your email and password.";
        this.showSnackbar = true;
      });

    console.log("Logged IN!");
  }

  // eslint-disable-next-line @typescript-eslint/no-explicit-any
  async logoutUser() {
    // if the server successfully logged out the user, remove the token from local storage
    localStorage.removeItem("token");
    this.$store.dispatch("logout");
    this.showLoginButton = true;
    this.showSignupButton = true;
    this.showLogoutButton = false;
    this.$router.push("/");
  }

  mounted() {
    if (localStorage.getItem("token")) {
      this.showLoginButton = false;
      this.showSignupButton = false;
      this.showLogoutButton = true;
    }
  }
}
</script>

<style scoped src="../assets/styles/header.css"></style>
