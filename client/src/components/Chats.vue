<template>
  <div id="chats">
    <Header />
    <div class="block">
      <div class="search-create__block">
        <div class="search_input">
          <input
            type="text"
            class="search_form"
            v-model="search"
            placeholder="Search"
          />
        </div>
        <form class="create_form" @submit.prevent="showCreateRoomModal">
          <input class="create_btn btn" type="submit" value="Create" />
        </form>
      </div>
      <div class="chat__list">
        <div v-for="room in rooms" :key="room.id">
          <ChatElement
            @join-chat="JoinChat"
            :chat_name="room.name"
            :chat_id="room.id"
            :owner_id="room.owner.id"
            :user_id="me.id"
            v-if="
              (room.name.includes(search) && search !== '') || search === ''
            "
          />
        </div>
      </div>
      <CreateRoomModal v-show="isModalVisible" @close="closeModal" />
    </div>
  </div>
</template>

<script>
import Header from "./Header.vue";
import ChatElement from "./ChatElement";
import CreateRoomModal from "./CreateRoomModal.vue";
import {
  JOIN_ROOM,
  GET_ROOMS,
  USER_INFO,
  SUB_ROOM_CREATED,
  SUB_ROOM_DELETED,
  SUB_ROOM_UPDATED,
  // LEAVE_ROOM
} from "@/graphql/graphql.js";

export default {
  name: "Chats",
  data() {
    return {
      search: "",
      isModalVisible: false,
      rooms: [],
      me: {},
    };
  },
  components: {
    Header,
    ChatElement,
    CreateRoomModal,
  },
  apollo: {
    $subscribe: {
      delete_room: {
        query: SUB_ROOM_DELETED,
        async result({ data }) {
          console.log("there" + data);
          let right_index = -1;
          this.rooms.forEach((item, index) =>
            item.id === data.roomDeleted.id ? (right_index = index) : ""
          );
          this.rooms.splice(right_index, 1);
          // const me = await this.$apollo.query({
          //   fetchPolicy: "no-cache",
          //   query: USER_INFO,
          // });
          // if (!me.data.me.currentRoom) {
          //   this.display_chat = false;
          //   this.display_list = true;
          // }
        },
      },

      create_rooms: {
        query: SUB_ROOM_CREATED,
        result({ data }) {
          this.rooms.push(data.roomCreated);
        },
      },

      rename_room: {
        query: SUB_ROOM_UPDATED,
        result({ data }) {
          if (data.roomUpdated.owner.id === this.me.id) {
            this.current_room_name = data.roomUpdated.name;
          }
          const new_rooms = this.rooms.map((item) => {
            item.id === data.roomUpdated.id ? (item = data.roomUpdated) : item;
            return item;
          });
          this.rooms = new_rooms;
        },
      },
      // },
    },
  },
  async created() {
    const me = await this.$apollo.query({
      fetchPolicy: "no-cache",
      query: USER_INFO,
    });
    if (me.data.me.currentRoom) {
      this.$router.push("/chat");
      // await this.OpenChat(me);
    }
    const rooms = await this.$apollo.query({
      query: GET_ROOMS,
    });
    this.rooms = rooms.data.rooms;
    this.me = me.data.me;
    console.log(rooms);
  },
  methods: {
    async showCreateRoomModal() {
      this.isModalVisible = true;
    },

    async closeModal() {
      this.isModalVisible = false;
    },

    async JoinChat(chat_id) {
      await this.$apollo.mutate({
        mutation: JOIN_ROOM,
        variables: {
          id: chat_id,
        },
      });
      this.$router.push("/chat");
    },
  },
};
</script>

<style scoped>
.block {
  align-content: center;
  display: block;
}

.search-create__block {
  justify-content: center;
  margin-right: auto;
  display: flex;
  margin-top: 90px;
}

.search_input {
  padding-right: 1.5em;
}

.search_form {
  width: 505px;
  height: 61px;
  border: 0.12em solid rgb(137, 137, 137);
  margin-bottom: 1.5em;
  padding-left: 14px;
}

input,
.btn {
  font-family: "Playfair Display", serif;
  font-size: 22px;
}

.btn {
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

input {
  transform: translate3d(0, -1px, 0);
}

.chat__list {
  overflow-y: auto;
  grid-area: chat__list;
  width: 670px;
  height: 700px;
  /* border: black 1px solid; */
  margin: auto;
}

.chat__list::-webkit-scrollbar {
  width: 22px;
  height: 72px;
}

.chat__list::-webkit-scrollbar-thumb {
  background-color: white;
  border: 2px solid rgb(137, 137, 137);
}
</style>