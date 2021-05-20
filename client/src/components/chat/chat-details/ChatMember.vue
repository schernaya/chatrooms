<template>
  <div class="chat_member">
    <div class="members_names" :class="user_id === member.id ? 'you' : ''">
      {{ member.username }} {{ user_id === member.id ? "(you)" : "" }}
    </div>
  </div>
</template>

<script>
export default {
  name: "ChatMember",
  props: ["member", "user_id"],
};
</script>

<style scoped>
.chat_member {
  display: inline-flex;
}

.chat_member div {
  padding-bottom: 20px;
  font-size: 24px;
}

.members_names.you {
  background-color: lavender;
}
</style>