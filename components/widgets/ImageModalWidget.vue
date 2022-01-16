<template>
  <div class="image-modal-widget" :class="{ open: showModal }">
    <div class="content">
      <button type="button" class="close-button" @click="closeModal">
        &times;
      </button>
      <div class="image-container">
        <!-- The v-if is added because it would be better if it were not showing (and loading) than the previous image -->
        <img v-if="showModal" :src="url" alt="" />
      </div>
      <div class="description">
        <p class="name">{{ name }}</p>
        <p class="location">{{ location }}</p>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  name: 'ImageModalWidget',

  model: {
    prop: 'showModal',
    event: 'input',
  },
  props: {
    showModal: {
      type: Boolean,
      default: false,
    },
    image: {
      type: Object,
      default: () => ({}),
    },
  },

  computed: {
    url(): string {
      return !this.image ? '' : (this.image as any).urls?.regular || ''
    },
    user(): any {
      return (this.image as any)?.user || {}
    },

    name(): string {
      return this.user.name
    },
    location(): any {
      return this.user.location
    },
  },
  methods: {
    closeModal() {
      this.$emit('input', false)
    },
  },
})
</script>

<style lang="scss" scoped>
// Open state
.image-modal-widget.open {
  pointer-events: unset;
  &::before {
    opacity: 1;
  }

  .content {
    transform: translateY(0);
    opacity: 1;
  }
}

// Default state is closed
.image-modal-widget {
  position: fixed;
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
  overflow-y: auto;
  z-index: 10;
  display: grid;
  place-items: center;
  padding: 3%;
  pointer-events: none;

  &::before {
    content: '';
    opacity: 0;
    position: absolute;
    top: 0;
    right: 0;
    left: 0;
    height: 100%;
    background: rgba(109, 109, 109, 0.4);
    backdrop-filter: blur(8px);

    box-shadow: -1px 1px 5px rgb(139 139 139 / 38%);

    transition: opacity 0.2s ease-in-out;
  }
}

.content {
  max-width: 700px;
  width: 100%;
  position: relative;
  z-index: 10;
  border-radius: 10px;
  overflow: hidden;
  background: white;

  transform: translateY(10%);
  opacity: 0;
  transition: transform 0.3s ease-in-out, opacity 0.2s ease-in-out;

  .close-button {
    border-radius: 100%;
    height: 40px;
    width: 40px;
    background: white;
    position: absolute;
    top: 1rem;
    right: 1rem;
    font-size: 1.5rem;
    font-weight: 500;
    color: rgb(88, 83, 83);
  }

  .image-container {
    width: 100%;
    // max-height: 400px;
    height: 400px;
    img {
      height: 100%;
      max-height: 100%;
      width: 100%;
      max-width: 100%;
      object-fit: cover;
      object-position: center;
    }
  }

  .description {
    padding: 2.5rem 3rem;

    .name {
      font-size: 1.2rem;
      font-weight: 500;
    }

    .location {
      font-size: 0.9rem;
      color: rgb(83, 81, 81);
    }
  }
}
</style>
