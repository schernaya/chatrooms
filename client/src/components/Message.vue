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
.author {
  display: flex;
  /* border: 2px solid rgb(137, 137, 137); */
}

.name-time-message__block {
  /* border: 2px solid rgb(29, 29, 29); */
  display: flex;
  width: 88%;
  margin-right: auto;
  position: relative;
}

.name-time__block {
  margin-right: auto;
  width: 100%;
  display: flex;
  position: absolute;
  margin-left: 5px;
  /* border: 2px solid rgb(196, 73, 73); */
}

.text {
  font-size: 22px;
  margin-left: 18px;
  margin-top: 40px;
  /* bottom: 0; */
  /* margin-top: 100px; */
  /* border: 2px solid rgb(112, 42, 42); */
  width: 100%;
  position: absolute;
}

.author__name,
.time {
  font-size: 24px;
  height: 27px;
}

.author__name {
  margin-left: 13px;
  color: #3d5a80;
  /* border: 2px solid rgb(137, 137, 137); */
}

.time {
  color: black;
  margin-left: auto;
  /* border: 2px solid rgb(137, 137, 137); */
}

.mess_wrap {
  /* width: inherit; */
  /* display: grid; */
  justify-content: left;
  margin-left: 10px;
  height: 110px;
  /* border: 2px solid rgb(29, 29, 29); */
}

.author__logo {
  font-size: 22px;
  background-color: black;
  color: white;
  /* margin-left: 10px; */
  margin-top: 1px;
  width: 55px;
  height: 55px;
  border-radius: 50%;
  justify-content: center;
  align-items: center;
  display: flex;
}

.message {
  font-size: large;
  color: #491c75;
  padding-bottom: 20px;
}

.mess_wrap .message {
  /* border: 2px solid rgb(29, 29, 29); */
  margin-bottom: 5px;
  /* overflow-wrap: anywhere; */
  width: 614px;
  padding: 10px;
  height: 80px;
}

.mess_wrap.own_message .message {
  background-color: lavender;
  /* margin-top: 20px; */
  /* bottom: 0; */
  /* border: 2px solid rgb(29, 29, 29); */
}
</style>
