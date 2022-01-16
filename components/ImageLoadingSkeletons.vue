<template>
  <section class="container">
    <div class="photo-main-container">
      <div v-for="(images, i) in displayingPhotosMatrix" :key="i">
        <div v-for="(image, j) in images" :key="j">
          <div
            class="image-loading-skeleton"
            :style="{ '--height': image.height + 'px' }"
          ></div>
        </div>
      </div>
    </div>
  </section>
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  name: 'ImageLoadingSkeletons',
  props: {
    columnCount: {
      type: Number,
      default: 3,
    },
  },
  data() {
    return {
      loadingPhotos: [
        { height: 200 },
        {
          height: 250,
        },
        {
          height: 170,
        },
        {
          height: 300,
        },

        {
          height: 190,
        },
        {
          height: 190,
        },
      ],
    }
  },
  computed: {
    displayingPhotosMatrix(): Array<Array<any>> {
      const returnVal = []

      const tempServerPhotos = [...this.loadingPhotos]

      while (tempServerPhotos.length > 0) {
        const lengthOfColumn = Math.ceil(
          tempServerPhotos.length / (this.columnCount - returnVal.length)
        )

        returnVal.push(tempServerPhotos.splice(0, lengthOfColumn))
      }

      return returnVal
    },
  },
})
</script>

<style lang="scss" scoped>
.image-loading-skeleton {
  --height: 0px;
  width: 100%;
  height: var(--height);
  background: #f5f5f5;
  border-radius: 5px;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  &::before {
    content: '';
    height: 1rem;
    width: 60%;
    background: #e6e6e6;
    display: block;
    border-radius: 4px;
    margin-bottom: 0.5rem;
  }

  &::after {
    content: '';
    height: 1rem;
    width: 40%;
    background: #e6e6e6;
    display: block;
    border-radius: 4px;
  }
}
</style>
