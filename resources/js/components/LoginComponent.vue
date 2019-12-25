<template>
  <v-app id="inspire">
    <v-content>
      <v-container class="fill-height" fluid>
        <v-row align="center" justify="center">
          <v-col cols="12" sm="8" md="4">
            <v-card class="elevation-12">
              <v-toolbar color="error" dark flat>
                <v-toolbar-title>Login Form</v-toolbar-title>
                <v-spacer />
              </v-toolbar>
              <v-card-text>
                <v-progress-linear
                  :active="loading"
                  :indeterminate="loading"
                  absolute
                  bottom
                  color="deep-purple accent-4"
                ></v-progress-linear>
                <v-form>
                  <v-text-field
                    color="error"
                    label="Login"
                    name="login"
                    v-model="email"
                    prepend-icon="mdi-email-outline"
                    type="email"
                  />

                  <v-text-field
                    color="error"
                    id="password"
                    label="Password"
                    v-model="password"
                    name="password"
                    prepend-icon="mdi-account-lock-outline"
                    type="password"
                  />
                </v-form>
              </v-card-text>

              <v-card-actions>
                <v-spacer />
                <v-btn color="error" @click="login">Login</v-btn>
              </v-card-actions>
            </v-card>
            <v-snackbar v-model="snackbar">
              {{ text }}
              <v-btn color="pink" text @click="snackbar = false">Close</v-btn>
            </v-snackbar>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      email: "",
      password: "",
      loading: false,
      snackbar: false,
      text: ""
    };
  },
  // created(){
  //     this.$vuetify.theme.dark = true;
  // },
  methods: {
    login: function() {
      // Add a request interceptor
      axios.interceptors.request.use(
        config => {
          this.loading = true;
          return config;
        },
        error => {
          this.loading = false;
          return Promise.reject(error);
        }
      );

      // Add a response interceptor
      axios.interceptors.response.use(
        response => {
          this.loading = false;
          return response;
        },
        error => {
          this.loading = false;
          return Promise.reject(error);
        }
      );
      axios
        .post("/api/login", { email: this.email, password: this.password })
        .then(res => {
          //console.dir(res);
          localStorage.setItem("token", res.data.token);
          this.$router
            .push("/admin")
            .then(res => console.log("LoggedIn Succesfully"))
            .catch(err => console.log(err));
        })
        .catch(err => {
          this.text = err.response.data.status;
          this.snackbar = true;
        });
    }
  }
};
</script>

<style scoped></style>
