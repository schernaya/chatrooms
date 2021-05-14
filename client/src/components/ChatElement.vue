<template>
  <div class="chat-list-element" @click="$emit('join-chat', chat_id)">
    <div class="info" v-bind:style= "[user_id === owner_id ? {'background-color': 'lavender'} : {'background-color': 'white'}]">
      <div class="chat__logo">
        {{ chatLetter }}
      </div>
      <p class="chat__name">{{ chat_name }}</p>
      <!-- <p  v-if="[user_id === owner_id]" class="chat__delete"> DELETE </p> -->
    </div>
  </div>
</template>

<script>
export default {
  name: "ChatListElement",
  props: ["chat_name", "chat_id", "owner_id", "user_id"],
  data() {
    return {
      active: false,
      chatLetter: "",
    };
  },
  created() {
    this.chatLetter = this.chat_name[0].toUpperCase();
  },
};
</script>

<style scoped>
.chat_list_element,
.info,
.chat__logo {
  display: flex;
}

.chat_list_element {
  overflow: hidden;
  justify-content: space-between;
}

.info {
  margin-right: auto;
  width: 646px;
  height: 61px;
  margin-bottom: 10px;
}

.info:hover {
  box-sizing: border-box;
  -moz-box-sizing: border-box;
  -webkit-box-sizing: border-box;
  border: 2px solid;
}

.chat__name,
.chat__logo {
  font-family: "Playfair Display", serif;
  font-size: 22px;
}

.chat__name {
  padding-left: 15px;
  transform: translate3d(0, -10px, 0);
}

.chat__logo {
  background-color: black;
  color: white;
  margin-left: 10px;
  margin-top: 1px;
  width: 55px;
  height: 55px;
  border-radius: 50%;
  justify-content: center;
  align-items: center;
}

.chat__delete {
  margin-left: 80px;
}
</style>