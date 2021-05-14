<template>
  <div class="login">
    <div class="block">
      <p class="title">Chatrooms</p>
      <p class="subtitle">Welcome!</p>
      <div class="error" v-if="error.length > 0">
        {{ error }}
      </div>
      <form class="login_form" @submit.prevent="GetRegistered">
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
        <input
          type="password"
          class="form_input"
          v-model="confirmedPassword"
          placeholder="Confirm password"
        />
        <div class="form_btn">
          <button class="register_btn btn">Continue</button>
        </div>
        <p class="subtitle">or</p>
      </form>
      <form class="login_form" @submit.prevent="LoginUser">
        <div class="form_btn">
          <button class="login_btn btn">Login</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import { CHECK_USERNAME, LOGIN, REGISTER } from "@/graphql/graphql";
import { onLogin } from "./../vue-apollo";

export default {
  name: "Registration",
  data() {
    return {
      token: "",
      login: "",
      password: "",
      confirmedPassword: "",
      error: "",
    };
  },
  methods: {
    async GetRegistered() {
      if (this.login.length === 0 || this.password.length === 0) {
        this.error = "Incorrect login input.";
      } else if (this.password.length !== this.confirmedPassword.length) {
        this.error = "The passwords didn't match.";
      } else {
        const user = await this.checkLoginIsUsed();
        if (user) {
          this.error = "The login is already used.";
        } else {
          const newUser = await this.AddNewUser();
          this.error = "";
          if (!newUser) {
            this.error = "Can't create new account.";
          }
          const token = await this.GetToken();
          if (token) {
            this.error = "";
            await onLogin(this.$apollo.provider.defaultClient, token);
            await this.$router.push("/chats");
          } else {
            this.error = "Can't get user's token.";
          }
        }
      }
    },

    async AddNewUser() {
      const user = await this.$apollo.query({
        query: REGISTER,
        variables: {
          username: this.login,
          password: this.password,
        },
      });
      return user;
    },

    async LoginUser() {
      this.$router.push("/");
    },

    async checkLoginIsUsed() {
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
  height: 82%;
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

.login_btn {
  margin-top: 0%;
  background-color: #3d5a80;
}

.login_btn:hover {
  background-color: #314764;
}

.error {
  text-align: left;
  margin-left: 3.3em;
  margin-bottom: 1em;
  color: crimson;
}
</style>
