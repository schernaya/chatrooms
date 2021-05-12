<template>
  <div class="header">
    <p class="site__title">Chatrooms</p>
    <div class="logout" @click.prevent="Logout">
      <p class="logout_btn">Logout</p>
    </div>
  </div>
</template>

<script>
import { onLogout } from "./../vue-apollo";
export default {
  name: "Header",
  methods: {
    async Logout() {
      await onLogout(this.$apollo.provider.defaultClient);
      this.$router.push("/");
    },
  },
};
</script>

<style scoped>
.header,
.logout {
  top: 0;
  height: 55px;
  position: absolute;
  display: block;
}

.header {
  left: 0;
  background-color: black;
  width: 100%;
}

.site__title,
.logout_btn {
  color: white;
  font-size: 22px;
  transform: translate3d(0, -10px, 0);
  vertical-align: middle;
}

.site__title {
  text-align: center;
}

.logout {
  cursor: pointer;
  right: 0;
  width: 122px;
  background-color: #3d5a80;
}

.logout:hover {
  background-color: #314764;
}

.logout_btn {
  text-decoration: none;
  text-align: center;
}
</style>