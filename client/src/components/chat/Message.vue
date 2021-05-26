<template>
  <div class="mess_wrap" :class="user_id === author.id ? 'own_message' : ''">
    <div class="message">
      <div class="author">
        <div class="author__logo">{{ author.username[0].toUpperCase() }}</div>
        <div class="name-time-message__block">
          <div class="name-time__block">
            <div class="author__name">{{ author.username }}</div>
            <div class="time">{{ messageTime }}</div>
          </div>
          <div class="text">
            {{ message }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ChatElement",
  props: ["message", "author", "messageTime", "user_id"],
};
</script>

<style scoped>
.author,
.name-time-message__block,
.name-time__block, 
.author__logo {
  display: flex;
}

.name-time-message__block {
  width: 88%;
  margin-right: auto;
  position: relative;
}

.name-time__block, .text {
  width: 100%;
  position: absolute;
}

.name-time__block {
  margin-right: auto;
  margin-left: 5px;
}

.text {
  font-size: 22px;
  margin-left: 18px;
  margin-top: 40px;
}

.author__name,
.time {
  font-size: 24px;
  height: 27px;
}

.author__name {
  margin-left: 13px;
  color: #3d5a80;
}

.time {
  color: black;
  margin-left: auto;
}

.mess_wrap {
  justify-content: left;
  margin-left: 10px;
  height: 110px;
}

.author__logo {
  font-size: 22px;
  background-color: black;
  color: white;
  margin-top: 1px;
  width: 55px;
  height: 55px;
  border-radius: 50%;
  justify-content: center;
  align-items: center;
}

.message {
  font-size: large;
  color: #491c75;
  padding-bottom: 20px;
}

.mess_wrap .message {
  margin-bottom: 5px;
  width: 614px;
  padding: 10px;
  height: 80px;
}

.mess_wrap.own_message .message {
  background-color: lavender;
}
</style>
