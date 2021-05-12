<template>
  <div class="chat_info">
    <h2>Chat creator</h2>
    <ChatOwner :owner="owner" :user_id="user_id" />
    <h2>Chat members</h2>
    <div class="chat_members">
      <div v-for="member in members" :key="member.id">
        <ChatMember :member="member" :user_id="user_id" />
      </div>
    </div>
  </div>
</template>

<script>
import ChatMember from "./ChatMember";
import ChatOwner from "./ChatOwner";
export default {
  name: "ChatInfo",
  components: {
    ChatMember,
    ChatOwner,
  },
  props: ["members", "owner", "user_id"],
};
</script>


<style scoped>
.chat_info {
  grid-area: chat_info;
}
.chat_info h2 {
  margin-top: 0px;
}
.chat_members {
  overflow-y: auto;
  height: 70%;
  position: absolute;
  width: 23%;
}
.chat_members::-webkit-scrollbar {
  width: 15px;
}
.chat_members::-webkit-scrollbar-track {
  border: 7px solid #232943;
  box-shadow: inset 0 0 2.5px 2px rgba(0, 0, 0, 0);
}
.chat_members::-webkit-scrollbar-thumb {
  background: linear-gradient(#e66465, #9198e5);
  border-radius: 3px;
}
</style>