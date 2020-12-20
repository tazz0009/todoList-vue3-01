<template>
  <div>
    <button @click="show = !show">
      Toggle render
    </button>

    <transition
      name="custom-classes-transition"
      enter-active-class="animate__animated animate__tada"
      leave-active-class="animate__animated animate__bounceOutRight"
    >
      <p v-if="show">hello</p>
    </transition>
  </div>
</template>

<script>
export default {
  data() {
    return {
      show: true,
    };
  },
};
</script>

<style>
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.compat.css");
</style>
