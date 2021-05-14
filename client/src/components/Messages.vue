<template>
  <div class="chat">
    <div class="messages">
      <div v-for="message in messages" :key="message.id">
        <Message
          :message="message.text"
          :messageTime="ValidateDate(message)"
          :author="message.author"
          :user_id="user_id"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Message from "./Message";
import {
  SUB_MESSAGE_CREATED,
  USER_INFO,
} from "@/graphql/graphql.js";

export default {
  name: "Chat",
  components: {
    Message,
  },
  props: ["messages", "user_id"],
  data() {
    return {
      message_text: "",
      me: {},
    };
  },
  apollo: {
    me: {
      fetchPolicy: "no-cache",
      query: USER_INFO,
    },
    $subscribe: {
      create_rooms: {
        query: SUB_MESSAGE_CREATED,
        result({ data }) {
          this.messages.push(data.messageCreated);
        },
      },
    },
  },
  methods: {
    ValidateDate(message) {
      const date = new Date(message.timestamp);
      return (
        date.getHours() +
        ":" +
        date.getMinutes() +
        ":" +
        date.getSeconds() +
        " " +
        date.getDate() +
        "/0" +
        (date.getMonth() + 1)
      );
    },
  },
};
</script>

<style scoped>
.messages {
  /* grid-area: messages; */
  /* overflow-y: auto; */
  height: 90%;
}

.send_btn {
  border-radius: 0.5em;
}
.send_btn img {
  width: 30px;
  height: 30px;
}
.send_mess {
  display: flex;
  width: inherit;
}

</style>