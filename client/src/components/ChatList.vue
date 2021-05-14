<template>
  <div class="chat_lists">
    <!-- <input type="text" v-model="search" class="search" /> -->
    <div class="chat_list">
      <!-- <div v-for="room in rooms" :key="room.id">
        <ChatElement
          @join-chat="JoinChat"
          :chat_name="room.name"
          :chat_id="room.id"
          :owner_id="room.owner.id"
          :user_id="user_id"
          v-if="(room.name.includes(search) && search !== '') || search === ''"
        />
      </div> -->
    </div>
  </div>
</template>


<script>
import ChatElement from "./ChatElement";
import { JOIN_ROOM } from "@/graphql/graphql.js";

export default {
  name: "ChatList",
  props: ["rooms", "user_id"],
  data() {
    return {
      active: false,
      search: "",
    };
  },
  // watch: {
  //   search: function () {
  //     this.rooms = this.rooms.filter((room) => room.name.includes(this.search));
  //   },
  // },
  components: {
    ChatElement,
  },
  methods: {
    async JoinChat(chat_id) {
      const chat_info = await this.$apollo.mutate({
        mutation: JOIN_ROOM,
        variables: {
          id: chat_id,
        },
      });
      this.$emit("chat-clicked", {
        data: chat_info.data,
      });
    },
  },
};
</script>

<style scoped>
/* input.search {
  margin-bottom: 10px;
  width: 95%;
  padding: 5px;
  border: 1px solid;
  font-size: larger;
  border-radius: 1rem;
} */
.chat_list {
  grid-area: chat_list;
  overflow-y: auto;
  height: 80%;
  position: absolute;
  width: 18%;
}

.chat_list::-webkit-scrollbar {
  width: 15px;
}
.chat_list::-webkit-scrollbar-track {
  border: 7px solid #232943;
  box-shadow: inset 0 0 2.5px 2px rgba(0, 0, 0, 0);
}
.chat_list::-webkit-scrollbar-thumb {
  background: linear-gradient(#e66465, #9198e5);
  border-radius: 3px;
}
</style>