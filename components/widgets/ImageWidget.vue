<template>
  <div class="image-widget" @click="$emit('open')">
    <img :src="image.urls.small" alt="" />
    <div class="info">
      <p class="name">{{ image.user.name }}</p>
      <p class="location">{{ image.user.location }}</p>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  name: 'ImageWidget',
  props: {
    image: {
      type: Object,
      required: true,
    },
  },
})
</script>

<style lang="scss" scoped>
.image-widget {
  width: 100%;
  border-radius: 6px;
  overflow: hidden;
  position: relative;
  cursor: pointer;
  &:hover {
    img {
      transform: scale(1.1);
    }
  }
  img {
    max-width: 100%;
    width: 100%;
    transform: scale(1);
    transition: transform ease-in-out 0.3s;
  }
}

.info {
  padding: 1.5rem;
  position: absolute;

  bottom: 0;
  left: 0;
  right: 0;
  color: white;
  background: linear-gradient(to top, black, rgba(0, 0, 0, 0.575), transparent);

  .name {
    font-size: 0.9rem;
    letter-spacing: 0.025rem;
  }

  .location {
    font-weight: 300;
    font-size: 0.8rem;
    letter-spacing: 0.05rem;
  }
}
</style>
