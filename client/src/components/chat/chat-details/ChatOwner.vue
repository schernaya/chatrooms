<template>
  <div class="owner">
    <div class="owner_name">
      <div class="text">
        {{ owner.username }} {{ user_id === owner.id ? "(you)" : "" }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ChatOwner",
  props: ["owner", "user_id"],
};
</script>
<style scoped>
.owner {
  display: flex;
  justify-content: space-between;
}
.owner_name {
  font-size: x-large;
  display: inline-flex;
}

</style>