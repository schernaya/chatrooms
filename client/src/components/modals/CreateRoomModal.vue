<template>
  <transition name="modal__new-room">
    <div class="background">
      <div class="block">
        <div class="header">
          <div class="modal__title">Create chat</div>
          <div class="modal__close" @click="Cancel">×</div>
        </div>
        <p class="error" v-if="error.length > 0">
          {{ error }}
        </p>
        <input
          type="text"
          class="chat-name"
          v-model="chatName"
          placeholder="Name"
        />
        <button class="continue_btn btn" @click="CreateChat">Continue</button>
        <button class="cancel_btn btn" @click="Cancel">Cancel</button>
      </div>
    </div>
  </transition>
</template>

<script>
import { CREATE_ROOM } from "@/graphql/graphql.js";
export default {
  name: "ModalCreateChat",
  data() {
    return {
      error: "",
      chatName: "",
      rooms: [],
    };
  },
  methods: {
    Cancel() {
      this.$emit("close");
      this.error = "";
    },

    async CreateChat() {
      if (this.chatName) {
        this.error = "";
        try {
          await this.$apollo.mutate({
            mutation: CREATE_ROOM,
            variables: {
              room_name: this.chatName,
            },
          });
          this.chatName = "";
          this.Cancel();
        } catch (err) {
          this.error = "The chat name already exists.";
        }
      } else {
        this.error = "Chat name should contain at least one letter.";
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
  height: 130px;
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
  padding: 35px 140px 10px 35px;
}

.modal__close {
  font-size: 64px;
  padding-top: 0;
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