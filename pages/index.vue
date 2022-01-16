<template>
  <div>
    <header class="container">
      <form action="" class="header-form">
        <div class="form-group">
          <input
            v-model="inputString"
            type="text"
            class="search-input"
            id="search-input"
            placeholder="Search for photo"
          />
          <label for="search-input" class="icon-container"
            ><svg
              width="16"
              height="16"
              viewBox="0 0 20 21"
              fill="none"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M19.7722 19.5854L14.8912 14.7124C16.1044 13.2558 16.8354 11.3854 16.8354 9.3474C16.8354 4.71326 13.0592 0.943115 8.41774 0.943115C3.77617 0.943115 0 4.71326 0 9.3474C0 13.9812 3.77617 17.7511 8.41774 17.7511C10.459 17.7511 12.3325 17.0214 13.7915 15.8102L18.6725 20.6832C18.8243 20.8348 19.0234 20.9106 19.2224 20.9106C19.4213 20.9106 19.6204 20.8348 19.7723 20.6832C20.0759 20.38 20.0759 19.8885 19.7722 19.5854ZM1.55517 9.3474C1.55517 5.56939 4.63369 2.49576 8.41774 2.49576C12.2017 2.49576 15.2801 5.56939 15.2801 9.3474C15.2801 13.1251 12.2017 16.1984 8.41774 16.1984C4.63369 16.1984 1.55517 13.1251 1.55517 9.3474Z"
                fill="currentColor"
              />
            </svg>
          </label>
        </div>
      </form>
      <transition name="fade">
        <p class="search-highlight" v-show="inputString">
          Search Results for <span class="keyword">"{{ inputString }}"</span>
        </p>
      </transition>
    </header>

    <image-loading-skeletons
      :columnCount="columnCount"
      v-show="$fetchState.pending"
    />
    <section class="container">
      <div class="photo-main-container" ref="photoMainContainer">
        <div
          v-for="(images, i) in displayingPhotosMatrix"
          :key="i"
          v-show="!$fetchState.pending"
        >
          <div v-for="image in images" :key="image.id">
            <image-widget @open="openImageModal(image)" :image="image" />
          </div>
        </div>
      </div>
    </section>

    <image-modal-widget :image="stagingImage" v-model="showModal" />
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import _, { entries } from 'lodash'
import ImageWidget from '~/components/widgets/ImageWidget.vue'
import ImageModalWidget from '~/components/widgets/ImageModalWidget.vue'
import ImageLoadingSkeletons from '~/components/ImageLoadingSkeletons.vue'

export default Vue.extend({
  components: { ImageWidget, ImageModalWidget, ImageLoadingSkeletons },
  name: 'IndexPage',
  data() {
    return {
      showModal: false,
      unsplashBaseUrl: 'https://api.unsplash.com',
      relativeSearchUrl: '/search/photos',
      defaultRelativePath: '/photos',
      inputString: '',
      // the input string and the search strings are separate
      // because I want to debouce the value change and also maintain an
      // immediate update rate for the 'search results for ''' string in the
      // header
      searchString: '',
      stagingImage: undefined,
      serverPhotos: [],

      columnCount: 3,
      perPage: 15,
    }
  },
  async fetch() {
    console.log('fetching data')
    console.log(this.queryString)
    await this.$axios
      .$get(this.queryString, {
        headers: {
          Authorization: `Client-ID ${process.env.UNSPLASH_ACCESS_KEY}`,
        },
      })
      .then((response: any) => {
        this.serverPhotos = response.results || response
      })
      .catch((error: any) => {
        this.$nuxt.error({
          statusCode: error.response?.status || 404,
          message: 'Unable to fetch data from Unsplash',
        })
      })
  },
  mounted() {
    this.observeTheSizeOfPhotoContainer()
  },
  computed: {
    displayingPhotosMatrix(): Array<Array<any>> {
      const returnVal = []

      const tempServerPhotos = [...this.serverPhotos]

      while (tempServerPhotos.length > 0) {
        const lengthOfColumn = Math.ceil(
          tempServerPhotos.length / (this.columnCount - returnVal.length)
        )

        returnVal.push(tempServerPhotos.splice(0, lengthOfColumn))
      }

      return returnVal
    },
    queryString(): string {
      return (
        this.unsplashBaseUrl +
        (this.searchString
          ? // If there is a search string, use the search endpoint
            `${this.relativeSearchUrl}?per_page=${
              this.perPage
            }&query=${encodeURI(this.searchString)}`
          : // if not use the photos endpoint
            `${this.defaultRelativePath}?orientation=portrait&per_page=${this.perPage}`)
      )
    },
  },
  methods: {
    observeTheSizeOfPhotoContainer() {
      const container = this.$refs.photoMainContainer

      const resizeObserver = new ResizeObserver((entries) => {
        entries.forEach((entry) => {
          if (entry.contentBoxSize) {
            // Firefox implements `contentBoxSize` as a single content rect, rather than an array
            const contentBoxSize = Array.isArray(entry.contentBoxSize)
              ? entry.contentBoxSize[0]
              : entry.contentBoxSize

            // Check if the size of the content size is less than or equal to 500
            if (contentBoxSize.inlineSize <= 700) {
              this.columnCount = 2
            } else {
              this.columnCount = 3
            }
          }
        })
      })

      resizeObserver.observe(container)
    },
    handleSearchInput: _.debounce(function (value: string) {
      // @ts-ignore
      this.searchString = value
    }, 500),
    openImageModal(image: any) {
      console.log('Open the modal')
      this.stagingImage = image
      this.showModal = true
    },
  },
  watch: {
    queryString() {
      this.$fetch()
    },
    inputString(value: string) {
      this.handleSearchInput(value)
    },
  },
})
</script>

<style lang="scss" scoped>
header.container {
  background: #dce2e9;
  padding-top: 5rem;
  padding-bottom: 5rem;
}

.form-group {
  // 20px for the allowance for the input search icon
  --search-icon-width: 28px;

  display: grid;
  grid-template-columns: var(--search-icon-width) 1fr;

  // Icon container
  & > .icon-container {
    grid-row: 1;
    grid-column: 1;
    opacity: 0.6;
    height: 100%;
    display: grid;
    place-items: center;
  }
  // search input
  & > .search-input {
    grid-row: 1;
    grid-column: 1 / -1;
    background: white;
    padding-left: calc(var(--search-icon-width) + 0.5rem);
    height: 40px;
    border-radius: 4px;
    font-size: 14px;
    &:focus,
    &:focus-visible {
      outline: #788498 1px solid;
    }
  }
}

.search-highlight {
  margin-top: 2rem;
  font-weight: 500;
  font-size: 2rem;

  .keyword {
    color: #788498;
  }
}
</style>
