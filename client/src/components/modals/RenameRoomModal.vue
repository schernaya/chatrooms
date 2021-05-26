<template>
  <transition name="modal__new-room">
    <div class="background">
      <div class="block">
        <div class="header">
          <div class="modal__title">Rename chat</div>
          <div class="modal__close" @click="Cancel">×</div>
        </div>
        <p class="error" v-if="error.length > 0">
          {{ error }}
        </p>
        <input
          type="text"
          class="chat-name"
          v-model="current"
          placeholder="Name"
        />
        <button class="continue_btn btn" @click="RenameChat">Continue</button>
        <button class="cancel_btn btn" @click="Cancel">Cancel</button>
      </div>
    </div>
  </transition>
</template>

<script>
import { UPDATE_ROOM, USER_INFO } from "@/graphql/graphql.js";
export default {
  name: "ModalRenameChat",
  props: ["room_name"],
  data() {
    return {
      rooms: [],
      error: false,
      current: "",
      error_name: false,
    };
  },
  created() {
    this.current = this.room_name;
  },
  watch: {
    current: function () {
      this.error_name = false;
      this.error = false;
    },
    room_name: function () {
      this.error_name = false;
      this.current = this.room_name;
    },
  },
  methods: {
    async Cancel() {
      this.error = false;
      this.current = this.room_name;
      this.error_name = false;
      this.$emit("close");
    },
    
    async RenameChat() {
      if (this.current) {
        this.error_name = false;
        this.error = false;
        const user_info = await this.$apollo.query({
          fetchPolicy: "no-cache",
          query: USER_INFO,
        });
        try {
          await this.$apollo.mutate({
            mutation: UPDATE_ROOM,
            variables: {
              id: user_info.data.me.currentRoom.id,
              name: this.current,
            },
          });
          this.current = "";
          this.Cancel();
        } catch (err) {
          this.error_name = true;
        }
      } else {
        this.error = true;
        this.error_name = false;
      }
    },
  },
};
</script>

<style scoped>
.background {
  top: 0;
  left: 0;
  background-color: rgba(0, 0, 0, 0.3);
  background-size: 100%;
  width: 100%;
  height: 100%;
  position: absolute;
  display: flex;
}

.block {
  display: block;
  text-align: center;
  align-content: center;
  width: 557px;
  height: 521px;
  margin: 130px auto;
  background-color: white;
  justify-content: center;
  align-items: center;
}

.header {
  display: flex;
  height: 140px;
  margin-bottom: 2em;
}

.chat-name,
.btn,
.error {
  font-family: "Playfair Display", serif;
  font-size: 22px;
}

.chat-name {
  width: 461px;
  height: 61px;
  border: 0.12em solid rgb(137, 137, 137);
  margin-bottom: 1.5em;
  padding-left: 14px;
}

.modal__title,
.modal__close {
  margin-top: 0;
  font-size: 64px;
}

.modal__title {
  padding-left: 35px;
  padding-top: 30px;
  padding-right: 90px;
}

.modal__close {
  font-size: 64px;
  padding-top: 0;
  float: right;
}

.modal__close,
.btn {
  cursor: pointer;
}

.btn {
  color: white;
  width: 485px;
  height: 61px;
  margin-bottom: 25px;
}

.continue_btn {
  background-color: black;
}

.cancel_btn {
  background-color: #3d5a80;
}

.continue_btn:hover {
  background-color: rgb(51, 51, 51);
}

.cancel_btn:hover {
  background-color: #314764;
}

.error {
  margin-left: 1.7em;
  text-align: left;
  color: crimson;
}
</style>