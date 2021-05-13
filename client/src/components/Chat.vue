<template>
  <div id="chat">
    <Header />
    <div class="blocks">
      <div class="buttons__block"></div>
      <div class="center__block">
        <div class="main__block">
          <div class="head__block">
            <div class="title__block">
              <div class="chat__title">{{ room.name }}</div>
            </div>
            <form class="left_form" @submit.prevent="LeftRoom">
              <input class="left_btn btn" type="submit" value="Left room" />
            </form>
          </div>
        </div>
        <div class="messages__list">
          <Messages v-bind:messages="messages" :user_id="me.id" />
        </div>
        <div class="input-message__wrap">
          <div class="input-message">
            <form class="send-message" @submit.prevent="SendMessage">
              <input
                type="text"
                class="form_control"
                v-model="message_text"
                placeholder="Message..."
              />
            </form>
          </div>
        </div>
      </div>
      <div class="members__block">
        <ChatData
          v-bind:members="members"
          v-bind:owner="owner"
          :user_id="me.id"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Header from "./Header.vue";
import Messages from "./Messages.vue";
import ChatData from "./ChatData.vue";
import {
  USER_INFO,
  CREATE_MESSAGE,
  LEAVE_ROOM,
  SUB_MEMBER_JOINED,
  SUB_MEMBER_LEFT,
} from "@/graphql/graphql.js";

export default {
  name: "Chats",
  data() {
    return {
      //   search: "",
      room_name: "",
      messages: [],
      members: [],
      room: [],
      owner: "",
      me: {},
      display_owner_buttons: false,
      message_text: "",
    };
  },
  components: {
    Header,
    Messages,
    ChatData,
    // ChatElement,
  },
  async created() {
    const me = await this.$apollo.query({
      fetchPolicy: "no-cache",
      query: USER_INFO,
    });
    this.me = me.data.me;
    this.room = me.data.me.currentRoom;
    this.messages = this.room.lastMessages;
    this.members = this.room.members;
    this.owner = this.room.owner;
  },
  apollo: {
    $subscribe: {
      joined_room: {
        query: SUB_MEMBER_JOINED,
        result({ data }) {
          this.members.push(data.memberJoined);
        },
      },
      left_room: {
        query: SUB_MEMBER_LEFT,
        result({ data }) {
          let right_index = -1;
          this.members.forEach((item, index) =>
            item.id === data.memberLeft.id ? (right_index = index) : ""
          );
          this.members.splice(right_index, 1);
        },
      },
    },
  },
  methods: {
    async SendMessage() {
      if (this.message_text) {
        await this.$apollo.mutate({
          mutation: CREATE_MESSAGE,
          variables: {
            message: this.message_text,
          },
        });
        this.message_text = "";
      }
    },
    async LeftRoom() {
      await this.$apollo.mutate({
        mutation: LEAVE_ROOM,
      });
      this.$router.push("/chats");
      // this.display_chat = false;
      // this.display_list = true;
      // this.display_owner_buttons = false;
    },
  },
};
</script>

<style scoped>
.blocks {
  display: flex;
}

.buttons__block {
  width: 100%;
  float: left;
}

.main__block {
  margin-right: auto;
  float: center;
}

.members__block {
  float: right;
  margin-top: 90px;
  margin-left: 60px;
  margin-right: 60px;
  width: 100%;
  height: 600px;
}

.center__block {
  float: center;
  width: 100%;
}

.main__block,
.input-message__wrap {
  align-content: center;
  display: block;
}

.head__block {
  justify-content: center;
  margin-right: auto;
  display: flex;
  margin-top: 90px;
}

.title__block {
  margin-right: 1.5em;
  width: 516px;
  height: 64px;
  border: 2px solid rgb(137, 137, 137);
  margin-bottom: 1.5em;
  text-align: center;
}

.chat__title {
  transform: translate3d(0, -6px, 0);
  font-size: 48px;
}

.chat__title,
.btn,
.form_control {
  font-family: "Playfair Display", serif;
}

.btn {
  font-size: 22px;
  width: 122px;
  height: 68px;
  border: 0;
  cursor: pointer;
  background-color: #3d5a80;
  color: white;
}

.btn:hover {
  background-color: #314764;
}

.messages__list {
  overflow-y: auto;
  grid-area: messages__list;
  width: 670px;
  height: 650px;
  margin: auto;
}

.messages__list::-webkit-scrollbar {
  width: 22px;
  height: 72px;
}

.messages__list::-webkit-scrollbar-thumb {
  background-color: white;
  border: 2px solid rgb(137, 137, 137);
}

.input-message,
.form_control {
  width: 640px;
}

.input-message {
  /* margin-left: 10px; */
  justify-content: center;
  padding-right: 15px;
  margin: auto;
}

.send-message {
  display: flex;
}

.form_control {
  font-size: 24px;
  margin: auto;
  border: 2px solid rgb(137, 137, 137);
  padding: 15px;
}
</style>