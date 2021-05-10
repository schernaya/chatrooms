<template>
  <div class="login">
    <div class="block">
      <p class="title">Chatrooms</p>
      <p class="subtitle">Welcome back!</p>
      <div class="error" v-if="error">This login doesn't have an account.</div>
      <form class="login_form" @submit.prevent="LoginUser">
        <input
          type="text"
          class="form_input"
          v-model="login"
          placeholder="Login"
        />
        <input
          type="password"
          class="form_input"
          v-model="password"
          placeholder="Password"
        />
        <div class="form_btn">
          <button class="login_btn btn">Continue</button>
        </div>
        <p class="subtitle">or</p>
      </form>
      <form class="login_form" @submit.prevent="RegisterUser">
        <div class="form_btn">
          <button class="register_btn btn">Create account</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import { LOGIN, CHECK_USERNAME } from "@/graphql/graphql.js";
import { onLogin } from "./../vue-apollo";

export default {
  name: "Login",
  data() {
    return {
      token: "",
      login: "",
      password: "",
      error: false,
    };
  },
  methods: {
    async RegisterUser() {
      this.$router.push("/registration");
    },
    
    async LoginUser() {
      if (this.login.length === 0 || this.password.length === 0) {
        this.error = true;
      } else {
        const user = await this.CheckUser();
        if (user) {
          const token = await this.GetToken();
          if (token) {
            await onLogin(this.$apollo.provider.defaultClient, token);
            this.error = false;
            this.$router.push("/chats");
          }
          this.error = true;
        } else {
          this.error = true;
        }
      }
    },

    async CheckUser() {
      const user = await this.$apollo.query({
        query: CHECK_USERNAME,
        variables: {
          username: this.login,
        },
      });
      return user.data.exists;
    },

    async GetToken() {
      const token = await this.$apollo.query({
        query: LOGIN,
        variables: {
          username: this.login,
          password: this.password,
        },
      });
      return token.data.token;
    },
  },
};
</script>

<style scoped>
*,
*:before,
*:after {
  box-sizing: border-box;
}

.login {
  top: 0;
  left: 0;
  background-image: url("~@/assets/background.jpg");
  background-size: 100%;
  width: 100%;
  height: 100%;
  position: absolute;
}

.block {
  display: block;
  text-align: center;
  align-content: center;
  width: 30%;
  height: 75%;
  margin: 12vh auto;
  margin-top: 130px;
  background-color: white;
}

.title {
  color: black;
  font-size: 64px;
  padding-top: 42px;
  margin-bottom: 0%;
}

.subtitle {
  color: rgb(123, 123, 123);
  font-size: 24px;
  /* margin-bottom: 43px; */
}

.form_input,
.btn {
  width: 410px;
  height: 61px;
}

.form_input {
  border: 0.12em solid rgb(137, 137, 137);
  margin-bottom: 1.5em;
}

input,
.btn,
.error {
  font-family: "Playfair Display", serif;
  font-size: 22px;
}

input {
  padding-left: 14px;
  transform: translate3d(0, -1px, 0);
}

.btn {
  background-color: black;
  color: white;
}

.btn:hover {
  background-color: rgb(51, 51, 51);
}

.register_btn {
  margin-top: 0%;
  background-color: #3d5a80;
}

.register_btn:hover {
  background-color: #314764;
}

.error {
  text-align: left;
  margin-left: 3.3em;
  margin-bottom: 1em;
  color: crimson;
}
</style>
