<template>
  <div id="chat">
    <Header />
    <div class="blocks">
      <div class="buttons__block">
        <form
          class="button_form"
          v-if="display_owner_buttons"
          @submit.prevent="RenameRoomModal"
        >
          <div class="form_btn">
            <button class="rename_btn left__btn">Rename</button>
          </div>
        </form>
      </div>
      <div class="center__block">
        <div class="main__block">
          <div class="head__block">
            <div class="title__block">
              <div
                ref="chat__title"
                :class="width_box <= width_box_text ? 'animate' : ''">{{ room_name }}</div
              >
              <div class="fader fader-left"></div>
              <div class="fader fader-right"></div>
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
    <RenameRoomModal
      v-show="isModalVisible"
      @close="closeModal"
      :room_name="room_name"
    />
  </div>
</template>

<script>
import Header from "../Header.vue";
import Messages from "./Messages.vue";
import ChatData from "./chat-details/ChatData.vue";
import RenameRoomModal from "../modals/RenameRoomModal.vue";

import {
  USER_INFO,
  CREATE_MESSAGE,
  LEAVE_ROOM,
  SUB_MEMBER_JOINED,
  SUB_MEMBER_LEFT,
  DELETE_ROOM,
  SUB_ROOM_UPDATED,
  SUB_ROOM_CREATED,
} from "@/graphql/graphql.js";

export default {
  name: "Chats",
  data() {
    return {
      room_name: "",
      messages: [],
      members: [],
      room: [],
      owner: "",
      me: {},
      display_owner_buttons: false,
      message_text: "",
      isModalVisible: false,
      width_box: 0,
      width_box_text: 516,
    };
  },
  components: {
    Header,
    Messages,
    ChatData,
    RenameRoomModal,
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
    this.room_name = this.room.name;
    if (this.me.id == this.owner.id) {
      this.display_owner_buttons = true;
    }
      this.width_box_text = this.$refs.chat__title.clientWidth;
  },
  async updated() {
    if (this.$refs.chat__title) {
      this.width_box_text = this.$refs.chat__title.clientWidth;
    }
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
      rename_room: {
        query: SUB_ROOM_UPDATED,
        result({ data }) {
          if (data.roomUpdated.owner.id === this.me.id) {
            this.room_name = data.roomUpdated.name;
          }
          const new_rooms = this.rooms.map((item) => {
            item.id === data.roomUpdated.id ? (item = data.roomUpdated) : item;
            return item;
          });
          this.rooms = new_rooms;
        },
      },
      create_rooms: {
        query: SUB_ROOM_CREATED,
        result({ data }) {
          this.rooms.push(data.roomCreated);
        },
      }
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
      this.$router.push("/chats").catch(() => {});
      this.display_owner_buttons = false;
    },

    async DeleteRoom() {
      const user_info = await this.$apollo.query({
        fetchPolicy: "no-cache",
        query: USER_INFO,
      });
      await this.$apollo.mutate({
        mutation: DELETE_ROOM,
        variables: {
          id: user_info.data.me.currentRoom.id,
        },
      });
      this.display_owner_buttons = false;
    },

    async RenameRoomModal() {
      const me = await this.$apollo.query({
        fetchPolicy: "no-cache",
        query: USER_INFO,
      });
      this.room_name = me.data.me.currentRoom.name;
      this.isModalVisible = true;
    },

    closeModal() {
      this.isModalVisible = false;
    },
  },
};
</script>

<style scoped>
.blocks {
  display: flex;
}

.buttons__block {
  margin-top: 90px;
  margin-left: 100px;
  width: 100%;
  float: left;
}

.left__btn {
  text-align: left;
  padding-left: 25%;
  width: 351px;
  height: 61px;
  background-color: white;
  font-size: 30px;
  border: 2px solid rgb(255, 255, 255);
  margin-bottom: 40px;
}

.left__btn:hover {
  border: 2px solid rgb(0, 0, 0);
  background-color: lavender;
  border: 0;
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
  padding-left: 15px;
  margin-right: 15px;
  width: 516px;
  border: 2px solid rgb(137, 137, 137);
  white-space: nowrap;
  overflow: hidden;
  font-size: 48px;
  height: 64px;
  margin-bottom: 15px;
}

.title__block,
.btn,
.form_control,
.left__btn {
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

.title__block div {
  display: inline-block;
}

.title__block .animate:hover {
  position: relative;
  animation: leftright 10s infinite alternate linear;
}
@keyframes leftright {
  0%,
  20% {
    transform: translateX(0%);
    left: 0%;
  }
  20%,
  40% {
    transform: translateX(-40%);
    left: 40%;
  }
  40%,
  60% {
    transform: translateX(-60%);
    left: 60%;
  }
  60%,
  80% {
    transform: translateX(-80%);
    left: 80%;
  }
  80%,
  100% {
    transform: translateX(-100%);
    left: 100%;
  }
}

.fader {
  position: absolute;
  top: 0;
  height: 100%;
  width: 25px;
}
.fader.fader-left {
  left: 0;
}

.fader.fader-right {
  right: 0;
}
</style>