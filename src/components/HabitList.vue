<template>
  <div>
    <p class="username">{{username}} Taco</p>
  </div>
</template>

<script>
export default {
  props: {
    username: {
      type: String,
      required: true
    }
  }
};
</script>

<style scoped>
.username {
  font-size: 50pt;
  color: red;
}
</style>
