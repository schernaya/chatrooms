<template>
  <div class="chat_info">
    <div class="chat_info_central_block">
      <div class="title title-creator">Creator</div>
      <ChatOwner :owner="owner" :user_id="user_id" />
      <div class="title title-members">Members</div>
      <div class="chat_members">
        <div v-for="member in members" :key="member.id">
          <ChatMember :member="member" :user_id="user_id" />
        </div>
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
  display: flex;
  justify-content: center;
}

.chat_info_central_block {
  padding-right: 200px;
}

.title {
  font-family: "Playfair Display", serif;
  font-size: 40px;
  margin-bottom: 15px;
}

.title-members {
  margin-top: 45px;
}

.chat_members {
  overflow-y: auto;
  height: 550px;
  position: absolute;
  width: 350px;
}

.chat_members::-webkit-scrollbar {
  width: 22px;
  height: 72px;
}

.chat_members::-webkit-scrollbar-thumb {
  background-color: white;
  border: 2px solid rgb(137, 137, 137);
}
</style>