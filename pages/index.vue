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
    <section class="loading-section" v-show="true"></section>
    <section class="container" v-show="true">
      <div class="photo-main-container" ref="photoMainContainer">
        <div v-for="(images, i) in displayingPhotosMatrix" :key="i">
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

export default Vue.extend({
  components: { ImageWidget, ImageModalWidget },
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
      serverPhotos: [
        {
          id: 'Qrjx2nTBHVo',
          created_at: '2020-07-01T18:30:14-04:00',
          updated_at: '2022-01-15T19:12:44-05:00',
          promoted_at: null,
          width: 6016,
          height: 4016,
          color: '#a6a6a6',
          blur_hash: 'LSJbHbIAIU%L_N%LRQxut7IUtRoM',
          description: null,
          alt_description: 'silver laptop on brown wooden table',
          urls: {
            raw: 'https://images.unsplash.com/photo-1593642532009-6ba71e22f468?ixid=MnwyOTE2NzZ8MXwxfGFsbHwxfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1',
            full: 'https://images.unsplash.com/photo-1593642532009-6ba71e22f468?crop=entropy&cs=srgb&fm=jpg&ixid=MnwyOTE2NzZ8MXwxfGFsbHwxfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=85',
            regular:
              'https://images.unsplash.com/photo-1593642532009-6ba71e22f468?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MXwxfGFsbHwxfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=1080',
            small:
              'https://images.unsplash.com/photo-1593642532009-6ba71e22f468?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MXwxfGFsbHwxfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=400',
            thumb:
              'https://images.unsplash.com/photo-1593642532009-6ba71e22f468?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MXwxfGFsbHwxfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=200',
          },
          links: {
            self: 'https://api.unsplash.com/photos/Qrjx2nTBHVo',
            html: 'https://unsplash.com/photos/Qrjx2nTBHVo',
            download:
              'https://unsplash.com/photos/Qrjx2nTBHVo/download?ixid=MnwyOTE2NzZ8MXwxfGFsbHwxfHx8fHx8Mnx8MTY0MjM2MjA0NQ',
            download_location:
              'https://api.unsplash.com/photos/Qrjx2nTBHVo/download?ixid=MnwyOTE2NzZ8MXwxfGFsbHwxfHx8fHx8Mnx8MTY0MjM2MjA0NQ',
          },
          categories: [],
          likes: 903,
          liked_by_user: false,
          current_user_collections: [],
          sponsorship: {
            impression_urls: [
              'https://ad.doubleclick.net/ddm/trackimp/N1153793.3286893UNSPLASH/B26742675.318699742;dc_trk_aid=511264654;dc_trk_cid=160420310;ord=[timestamp];dc_lat=;dc_rdid=;tag_for_child_directed_treatment=;tfua=;gdpr=${GDPR};gdpr_consent=${GDPR_CONSENT_755};ltd=?',
              'https://tps.doubleverify.com/visit.jpg?ctx=569086&cmp=26742675&sid=6781114&plc=318699742&adsrv=1&btreg=&btadsrv=&crt=&tagtype=&dvtagver=6.1.img&',
              'https://secure.insightexpressai.com/adServer/adServerESI.aspx?script=false&bannerID=9537089&rnd=[timestamp]&gdpr=&gdpr_consent=&redir=https://secure.insightexpressai.com/adserver/1pixel.gif',
              'https://secure.insightexpressai.com/adServer/adServerESI.aspx?script=false&bannerID=9541344&rnd=[timestamp]&redir=https://secure.insightexpressai.com/adserver/1pixel.gif',
            ],
            tagline: 'Designed to be the Best',
            tagline_url:
              'https://ad.doubleclick.net/ddm/trackclk/N1153793.3286893UNSPLASH/B26742675.318699742;dc_trk_aid=511264654;dc_trk_cid=160420310;dc_lat=;dc_rdid=;tag_for_child_directed_treatment=;tfua=;ltd=',
            sponsor: {
              id: '2DC3GyeqWjI',
              updated_at: '2022-01-16T14:32:25-05:00',
              username: 'xps',
              name: 'XPS',
              first_name: 'XPS',
              last_name: null,
              twitter_username: 'Dell',
              portfolio_url: 'http://www.dell.com/xps',
              bio: 'Designed to be the best, with cutting edge technologies, exceptional build quality, unique materials and powerful features.',
              location: null,
              links: {
                self: 'https://api.unsplash.com/users/xps',
                html: 'https://unsplash.com/@xps',
                photos: 'https://api.unsplash.com/users/xps/photos',
                likes: 'https://api.unsplash.com/users/xps/likes',
                portfolio: 'https://api.unsplash.com/users/xps/portfolio',
                following: 'https://api.unsplash.com/users/xps/following',
                followers: 'https://api.unsplash.com/users/xps/followers',
              },
              profile_image: {
                small:
                  'https://images.unsplash.com/profile-1600096866391-b09a1a53451aimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=32&w=32',
                medium:
                  'https://images.unsplash.com/profile-1600096866391-b09a1a53451aimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=64&w=64',
                large:
                  'https://images.unsplash.com/profile-1600096866391-b09a1a53451aimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=128&w=128',
              },
              instagram_username: 'dell',
              total_collections: 0,
              total_likes: 0,
              total_photos: 22,
              accepted_tos: true,
              for_hire: false,
              social: {
                instagram_username: 'dell',
                portfolio_url: 'http://www.dell.com/xps',
                twitter_username: 'Dell',
                paypal_email: null,
              },
            },
          },
          topic_submissions: {
            technology: {
              status: 'approved',
              approved_on: '2020-09-21T13:39:53-04:00',
            },
          },
          user: {
            id: '2DC3GyeqWjI',
            updated_at: '2022-01-16T14:32:25-05:00',
            username: 'xps',
            name: 'XPS',
            first_name: 'XPS',
            last_name: null,
            twitter_username: 'Dell',
            portfolio_url: 'http://www.dell.com/xps',
            bio: 'Designed to be the best, with cutting edge technologies, exceptional build quality, unique materials and powerful features.',
            location: null,
            links: {
              self: 'https://api.unsplash.com/users/xps',
              html: 'https://unsplash.com/@xps',
              photos: 'https://api.unsplash.com/users/xps/photos',
              likes: 'https://api.unsplash.com/users/xps/likes',
              portfolio: 'https://api.unsplash.com/users/xps/portfolio',
              following: 'https://api.unsplash.com/users/xps/following',
              followers: 'https://api.unsplash.com/users/xps/followers',
            },
            profile_image: {
              small:
                'https://images.unsplash.com/profile-1600096866391-b09a1a53451aimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=32&w=32',
              medium:
                'https://images.unsplash.com/profile-1600096866391-b09a1a53451aimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=64&w=64',
              large:
                'https://images.unsplash.com/profile-1600096866391-b09a1a53451aimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=128&w=128',
            },
            instagram_username: 'dell',
            total_collections: 0,
            total_likes: 0,
            total_photos: 22,
            accepted_tos: true,
            for_hire: false,
            social: {
              instagram_username: 'dell',
              portfolio_url: 'http://www.dell.com/xps',
              twitter_username: 'Dell',
              paypal_email: null,
            },
          },
        },
        {
          id: 's2QBTqUMHnc',
          created_at: '2022-01-15T04:00:22-05:00',
          updated_at: '2022-01-16T14:40:02-05:00',
          promoted_at: '2022-01-16T14:40:02-05:00',
          width: 4000,
          height: 2667,
          color: '#a6a6c0',
          blur_hash: 'LiFiZ1M{RjoL?wofayWVWst8ayof',
          description: 'Waterfall in the mist',
          alt_description: null,
          urls: {
            raw: 'https://images.unsplash.com/photo-1642236691454-43a1ac828286?ixid=MnwyOTE2NzZ8MHwxfGFsbHwyfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1',
            full: 'https://images.unsplash.com/photo-1642236691454-43a1ac828286?crop=entropy&cs=srgb&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHwyfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=85',
            regular:
              'https://images.unsplash.com/photo-1642236691454-43a1ac828286?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHwyfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=1080',
            small:
              'https://images.unsplash.com/photo-1642236691454-43a1ac828286?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHwyfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=400',
            thumb:
              'https://images.unsplash.com/photo-1642236691454-43a1ac828286?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHwyfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=200',
          },
          links: {
            self: 'https://api.unsplash.com/photos/s2QBTqUMHnc',
            html: 'https://unsplash.com/photos/s2QBTqUMHnc',
            download:
              'https://unsplash.com/photos/s2QBTqUMHnc/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHwyfHx8fHx8Mnx8MTY0MjM2MjA0NQ',
            download_location:
              'https://api.unsplash.com/photos/s2QBTqUMHnc/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHwyfHx8fHx8Mnx8MTY0MjM2MjA0NQ',
          },
          categories: [],
          likes: 0,
          liked_by_user: false,
          current_user_collections: [],
          sponsorship: null,
          topic_submissions: {},
          user: {
            id: 'jstvoUf7W1A',
            updated_at: '2022-01-16T14:40:02-05:00',
            username: 'chrisstenger',
            name: 'Chris Stenger',
            first_name: 'Chris',
            last_name: 'Stenger',
            twitter_username: null,
            portfolio_url: 'https://www.stenger.nl',
            bio: 'Nature and travel photographer from The Netherlands. Producer of audiovisuals. \r\nHope you enjoy my pictures. If you like what you see you can find more images on my website.',
            location: 'Huissen, The Netherlands',
            links: {
              self: 'https://api.unsplash.com/users/chrisstenger',
              html: 'https://unsplash.com/@chrisstenger',
              photos: 'https://api.unsplash.com/users/chrisstenger/photos',
              likes: 'https://api.unsplash.com/users/chrisstenger/likes',
              portfolio:
                'https://api.unsplash.com/users/chrisstenger/portfolio',
              following:
                'https://api.unsplash.com/users/chrisstenger/following',
              followers:
                'https://api.unsplash.com/users/chrisstenger/followers',
            },
            profile_image: {
              small:
                'https://images.unsplash.com/profile-1593425474781-da881f3adce8image?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=32&w=32',
              medium:
                'https://images.unsplash.com/profile-1593425474781-da881f3adce8image?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=64&w=64',
              large:
                'https://images.unsplash.com/profile-1593425474781-da881f3adce8image?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=128&w=128',
            },
            instagram_username: null,
            total_collections: 9,
            total_likes: 3,
            total_photos: 139,
            accepted_tos: true,
            for_hire: false,
            social: {
              instagram_username: null,
              portfolio_url: 'https://www.stenger.nl',
              twitter_username: null,
              paypal_email: null,
            },
          },
        },
        {
          id: 'lTBIifi0_Z8',
          created_at: '2022-01-15T06:58:37-05:00',
          updated_at: '2022-01-16T14:32:01-05:00',
          promoted_at: '2022-01-16T14:32:01-05:00',
          width: 2975,
          height: 3969,
          color: '#c0a68c',
          blur_hash: 'LQHn+]aeV[oelBxtWBaz0hRkt7WB',
          description:
            'Please like and follow: @mrfilipesantos ande @filipesantosphoto on Instagram.',
          alt_description: null,
          urls: {
            raw: 'https://images.unsplash.com/photo-1642247649933-a591a2f33562?ixid=MnwyOTE2NzZ8MHwxfGFsbHwzfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1',
            full: 'https://images.unsplash.com/photo-1642247649933-a591a2f33562?crop=entropy&cs=srgb&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHwzfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=85',
            regular:
              'https://images.unsplash.com/photo-1642247649933-a591a2f33562?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHwzfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=1080',
            small:
              'https://images.unsplash.com/photo-1642247649933-a591a2f33562?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHwzfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=400',
            thumb:
              'https://images.unsplash.com/photo-1642247649933-a591a2f33562?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHwzfHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=200',
          },
          links: {
            self: 'https://api.unsplash.com/photos/lTBIifi0_Z8',
            html: 'https://unsplash.com/photos/lTBIifi0_Z8',
            download:
              'https://unsplash.com/photos/lTBIifi0_Z8/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHwzfHx8fHx8Mnx8MTY0MjM2MjA0NQ',
            download_location:
              'https://api.unsplash.com/photos/lTBIifi0_Z8/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHwzfHx8fHx8Mnx8MTY0MjM2MjA0NQ',
          },
          categories: [],
          likes: 1,
          liked_by_user: false,
          current_user_collections: [],
          sponsorship: null,
          topic_submissions: {},
          user: {
            id: 'b1qHQ2vZrYY',
            updated_at: '2022-01-16T14:37:25-05:00',
            username: 'filipesantosphoto',
            name: 'Filipe Santos',
            first_name: 'Filipe',
            last_name: 'Santos',
            twitter_username: 'mrfilipesantos',
            portfolio_url: 'http://www.filipesantosphoto.com',
            bio: 'Exploring the world of photography.',
            location: 'Portugal',
            links: {
              self: 'https://api.unsplash.com/users/filipesantosphoto',
              html: 'https://unsplash.com/@filipesantosphoto',
              photos: 'https://api.unsplash.com/users/filipesantosphoto/photos',
              likes: 'https://api.unsplash.com/users/filipesantosphoto/likes',
              portfolio:
                'https://api.unsplash.com/users/filipesantosphoto/portfolio',
              following:
                'https://api.unsplash.com/users/filipesantosphoto/following',
              followers:
                'https://api.unsplash.com/users/filipesantosphoto/followers',
            },
            profile_image: {
              small:
                'https://images.unsplash.com/profile-1642248023970-edd63e5bc41fimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=32&w=32',
              medium:
                'https://images.unsplash.com/profile-1642248023970-edd63e5bc41fimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=64&w=64',
              large:
                'https://images.unsplash.com/profile-1642248023970-edd63e5bc41fimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=128&w=128',
            },
            instagram_username: 'filipesantosphoto',
            total_collections: 0,
            total_likes: 0,
            total_photos: 10,
            accepted_tos: true,
            for_hire: false,
            social: {
              instagram_username: 'filipesantosphoto',
              portfolio_url: 'http://www.filipesantosphoto.com',
              twitter_username: 'mrfilipesantos',
              paypal_email: null,
            },
          },
        },
        {
          id: 'DpMzZFt18r0',
          created_at: '2022-01-15T02:33:57-05:00',
          updated_at: '2022-01-16T14:24:01-05:00',
          promoted_at: '2022-01-16T14:24:01-05:00',
          width: 5855,
          height: 3903,
          color: '#402626',
          blur_hash: 'LNDcBxMx00%MtRWBnhoeIVkDV?NH',
          description: 'OH! what the duck ',
          alt_description: null,
          urls: {
            raw: 'https://images.unsplash.com/photo-1642231877874-ce3e205f39c0?ixid=MnwyOTE2NzZ8MHwxfGFsbHw0fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1',
            full: 'https://images.unsplash.com/photo-1642231877874-ce3e205f39c0?crop=entropy&cs=srgb&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw0fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=85',
            regular:
              'https://images.unsplash.com/photo-1642231877874-ce3e205f39c0?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw0fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=1080',
            small:
              'https://images.unsplash.com/photo-1642231877874-ce3e205f39c0?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw0fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=400',
            thumb:
              'https://images.unsplash.com/photo-1642231877874-ce3e205f39c0?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw0fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=200',
          },
          links: {
            self: 'https://api.unsplash.com/photos/DpMzZFt18r0',
            html: 'https://unsplash.com/photos/DpMzZFt18r0',
            download:
              'https://unsplash.com/photos/DpMzZFt18r0/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHw0fHx8fHx8Mnx8MTY0MjM2MjA0NQ',
            download_location:
              'https://api.unsplash.com/photos/DpMzZFt18r0/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHw0fHx8fHx8Mnx8MTY0MjM2MjA0NQ',
          },
          categories: [],
          likes: 1,
          liked_by_user: false,
          current_user_collections: [],
          sponsorship: null,
          topic_submissions: {
            'food-drink': {
              status: 'unevaluated',
            },
          },
          user: {
            id: 'ZO8VmEYzN2Q',
            updated_at: '2022-01-16T14:32:26-05:00',
            username: 'maneksingh',
            name: 'Manek Singh',
            first_name: 'Manek',
            last_name: 'Singh',
            twitter_username: null,
            portfolio_url: null,
            bio: 'A chef capturing the beauty of the world through my lens ',
            location: 'Toronto, Canada',
            links: {
              self: 'https://api.unsplash.com/users/maneksingh',
              html: 'https://unsplash.com/@maneksingh',
              photos: 'https://api.unsplash.com/users/maneksingh/photos',
              likes: 'https://api.unsplash.com/users/maneksingh/likes',
              portfolio: 'https://api.unsplash.com/users/maneksingh/portfolio',
              following: 'https://api.unsplash.com/users/maneksingh/following',
              followers: 'https://api.unsplash.com/users/maneksingh/followers',
            },
            profile_image: {
              small:
                'https://images.unsplash.com/profile-1633372259320-51f6fc827fcc?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=32&w=32',
              medium:
                'https://images.unsplash.com/profile-1633372259320-51f6fc827fcc?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=64&w=64',
              large:
                'https://images.unsplash.com/profile-1633372259320-51f6fc827fcc?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=128&w=128',
            },
            instagram_username: 'iammaneksingh',
            total_collections: 0,
            total_likes: 3,
            total_photos: 26,
            accepted_tos: true,
            for_hire: true,
            social: {
              instagram_username: 'iammaneksingh',
              portfolio_url: null,
              twitter_username: null,
              paypal_email: null,
            },
          },
        },
        {
          id: 'awzVq3Vl944',
          created_at: '2022-01-16T06:21:55-05:00',
          updated_at: '2022-01-16T14:16:01-05:00',
          promoted_at: '2022-01-16T14:16:01-05:00',
          width: 5293,
          height: 3529,
          color: '#f3f3f3',
          blur_hash: 'LMQT7Wofof%M~qof9FRj9Fofayof',
          description: 'Tate & Shard',
          alt_description: null,
          urls: {
            raw: 'https://images.unsplash.com/photo-1642331820977-a8eaf378e5d3?ixid=MnwyOTE2NzZ8MHwxfGFsbHw1fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1',
            full: 'https://images.unsplash.com/photo-1642331820977-a8eaf378e5d3?crop=entropy&cs=srgb&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw1fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=85',
            regular:
              'https://images.unsplash.com/photo-1642331820977-a8eaf378e5d3?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw1fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=1080',
            small:
              'https://images.unsplash.com/photo-1642331820977-a8eaf378e5d3?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw1fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=400',
            thumb:
              'https://images.unsplash.com/photo-1642331820977-a8eaf378e5d3?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw1fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=200',
          },
          links: {
            self: 'https://api.unsplash.com/photos/awzVq3Vl944',
            html: 'https://unsplash.com/photos/awzVq3Vl944',
            download:
              'https://unsplash.com/photos/awzVq3Vl944/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHw1fHx8fHx8Mnx8MTY0MjM2MjA0NQ',
            download_location:
              'https://api.unsplash.com/photos/awzVq3Vl944/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHw1fHx8fHx8Mnx8MTY0MjM2MjA0NQ',
          },
          categories: [],
          likes: 0,
          liked_by_user: false,
          current_user_collections: [],
          sponsorship: null,
          topic_submissions: {},
          user: {
            id: '8bPV16JZtMw',
            updated_at: '2022-01-16T14:17:25-05:00',
            username: 'zafarrancho',
            name: 'Lex Guerra',
            first_name: 'Lex',
            last_name: 'Guerra',
            twitter_username: 'zafarrancho',
            portfolio_url: 'http://www.zafarrancho.com',
            bio: 'Mexican. Londoner. Designer. Art Director. Traveller. Food eater. Photographer\r\n If you love my images and feel like buying me a coffee: paypal.me/lexguerra',
            location: 'London',
            links: {
              self: 'https://api.unsplash.com/users/zafarrancho',
              html: 'https://unsplash.com/@zafarrancho',
              photos: 'https://api.unsplash.com/users/zafarrancho/photos',
              likes: 'https://api.unsplash.com/users/zafarrancho/likes',
              portfolio: 'https://api.unsplash.com/users/zafarrancho/portfolio',
              following: 'https://api.unsplash.com/users/zafarrancho/following',
              followers: 'https://api.unsplash.com/users/zafarrancho/followers',
            },
            profile_image: {
              small:
                'https://images.unsplash.com/profile-1625657168833-3af7bf0bbb7bimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=32&w=32',
              medium:
                'https://images.unsplash.com/profile-1625657168833-3af7bf0bbb7bimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=64&w=64',
              large:
                'https://images.unsplash.com/profile-1625657168833-3af7bf0bbb7bimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=128&w=128',
            },
            instagram_username: 'zafarrancho',
            total_collections: 1,
            total_likes: 92,
            total_photos: 23,
            accepted_tos: true,
            for_hire: false,
            social: {
              instagram_username: 'zafarrancho',
              portfolio_url: 'http://www.zafarrancho.com',
              twitter_username: 'zafarrancho',
              paypal_email: null,
            },
          },
        },
        {
          id: 'FV3GConVSss',
          created_at: '2018-04-26T12:07:35-04:00',
          updated_at: '2022-01-16T06:03:40-05:00',
          promoted_at: '2018-04-27T08:24:09-04:00',
          width: 3762,
          height: 2508,
          color: '#d9d9d9',
          blur_hash: 'LFLqe8QlM|x]~W4TtSVrpd.9DiW;',
          description: 'workspace',
          alt_description: 'black floor lamp on living room sofa',
          urls: {
            raw: 'https://images.unsplash.com/photo-1524758631624-e2822e304c36?ixid=MnwyOTE2NzZ8MXwxfGFsbHw2fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1',
            full: 'https://images.unsplash.com/photo-1524758631624-e2822e304c36?crop=entropy&cs=srgb&fm=jpg&ixid=MnwyOTE2NzZ8MXwxfGFsbHw2fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=85',
            regular:
              'https://images.unsplash.com/photo-1524758631624-e2822e304c36?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MXwxfGFsbHw2fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=1080',
            small:
              'https://images.unsplash.com/photo-1524758631624-e2822e304c36?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MXwxfGFsbHw2fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=400',
            thumb:
              'https://images.unsplash.com/photo-1524758631624-e2822e304c36?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MXwxfGFsbHw2fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=200',
          },
          links: {
            self: 'https://api.unsplash.com/photos/FV3GConVSss',
            html: 'https://unsplash.com/photos/FV3GConVSss',
            download:
              'https://unsplash.com/photos/FV3GConVSss/download?ixid=MnwyOTE2NzZ8MXwxfGFsbHw2fHx8fHx8Mnx8MTY0MjM2MjA0NQ',
            download_location:
              'https://api.unsplash.com/photos/FV3GConVSss/download?ixid=MnwyOTE2NzZ8MXwxfGFsbHw2fHx8fHx8Mnx8MTY0MjM2MjA0NQ',
          },
          categories: [],
          likes: 3105,
          liked_by_user: false,
          current_user_collections: [],
          sponsorship: {
            impression_urls: [
              'https://ad.doubleclick.net/ddm/trackimpi/N1224323.3286893UNSPLASH/B25600467.324550585;dc_trk_aid=517316273;dc_trk_cid=157532779;ord=[timestamp];dc_lat=;dc_rdid=;tag_for_child_directed_treatment=;tfua=;ltd=?',
              'https://tag.researchnow.com/t/beacon?pr=285833&adn=1&ca=25600467&si=6303199&pl=313516386&cr=506408943&did=176&ord=[timestamp]&gdpr=${GDPR}&gdpr_consent=${GDPR_CONSENT_110}&us_privacy=${US_PRIVACY}',
              'https://secure.insightexpressai.com/adServer/adServerESI.aspx?script=false&bannerID=8967585&rnd=[timestamp]&redir=https://secure.insightexpressai.com/adserver/1pixel.gif',
            ],
            tagline: 'For Growing Businesses',
            tagline_url:
              'https://ad.doubleclick.net/ddm/trackclk/N1224323.3286893UNSPLASH/B25600467.324550585;dc_trk_aid=517316273;dc_trk_cid=157532779;dc_lat=;dc_rdid=;tag_for_child_directed_treatment=;tfua=;ltd=',
            sponsor: {
              id: 'D-bxv1Imc-o',
              updated_at: '2022-01-16T02:31:58-05:00',
              username: 'mailchimp',
              name: 'Mailchimp',
              first_name: 'Mailchimp',
              last_name: null,
              twitter_username: 'Mailchimp',
              portfolio_url: 'https://mailchimp.com/',
              bio: 'The all-in-one Marketing Platform built for growing businesses.',
              location: null,
              links: {
                self: 'https://api.unsplash.com/users/mailchimp',
                html: 'https://unsplash.com/@mailchimp',
                photos: 'https://api.unsplash.com/users/mailchimp/photos',
                likes: 'https://api.unsplash.com/users/mailchimp/likes',
                portfolio: 'https://api.unsplash.com/users/mailchimp/portfolio',
                following: 'https://api.unsplash.com/users/mailchimp/following',
                followers: 'https://api.unsplash.com/users/mailchimp/followers',
              },
              profile_image: {
                small:
                  'https://images.unsplash.com/profile-1609545740442-928866556c38image?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=32&w=32',
                medium:
                  'https://images.unsplash.com/profile-1609545740442-928866556c38image?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=64&w=64',
                large:
                  'https://images.unsplash.com/profile-1609545740442-928866556c38image?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=128&w=128',
              },
              instagram_username: 'mailchimp',
              total_collections: 0,
              total_likes: 19,
              total_photos: 0,
              accepted_tos: false,
              for_hire: false,
              social: {
                instagram_username: 'mailchimp',
                portfolio_url: 'https://mailchimp.com/',
                twitter_username: 'Mailchimp',
                paypal_email: null,
              },
            },
          },
          topic_submissions: {
            'architecture-interior': {
              status: 'approved',
              approved_on: '2021-09-24T06:49:44-04:00',
            },
            interiors: {
              status: 'approved',
              approved_on: '2020-06-03T09:49:17-04:00',
            },
            'business-work': {
              status: 'approved',
              approved_on: '2020-04-06T10:20:15-04:00',
            },
          },
          user: {
            id: 'zYw2OJ152h8',
            updated_at: '2022-01-16T14:02:28-05:00',
            username: 'heftiba',
            name: 'Toa Heftiba',
            first_name: 'Toa',
            last_name: 'Heftiba',
            twitter_username: 'toaheftiba',
            portfolio_url: 'http://heftiba.co.uk/',
            bio: "ᴘʀᴏᴅᴜᴄᴛ | ꜰᴏᴏᴅ | ʟɪꜰᴇꜱᴛʏʟᴇ ᴘʜᴏᴛᴏɢʀᴀᴘʜᴇʀ • ᴀ ꜰᴀɴ ᴏꜰ ᴏᴅᴅ ᴛʜɪɴɢꜱ ᴀɴᴅ ɢᴏᴏᴅ ʜᴜᴍᴏᴜʀ. 🙂\r\n👉🏻ᴄʟɪᴄᴋ ᴛʜᴇ 'ᴄᴏʟʟᴇᴄᴛɪᴏɴꜱ' ᴛᴀʙ ʙᴇʟᴏᴡ ᴛᴏ ᴠɪᴇᴡ ᴍʏ ɪᴍᴀɢᴇꜱ ɪɴ ᴏʀɢᴀɴɪꜱᴇᴅ ꜰᴏʟᴅᴇʀꜱ.  ᴡʜʏ ɴᴏᴛ ꜱᴀʏ ʜᴇʟʟᴏ ⚡️ ɪɢ: @ʜᴇꜰᴛɪʙᴀ.ᴄᴏ.ᴜᴋ ",
            location: 'UK',
            links: {
              self: 'https://api.unsplash.com/users/heftiba',
              html: 'https://unsplash.com/@heftiba',
              photos: 'https://api.unsplash.com/users/heftiba/photos',
              likes: 'https://api.unsplash.com/users/heftiba/likes',
              portfolio: 'https://api.unsplash.com/users/heftiba/portfolio',
              following: 'https://api.unsplash.com/users/heftiba/following',
              followers: 'https://api.unsplash.com/users/heftiba/followers',
            },
            profile_image: {
              small:
                'https://images.unsplash.com/profile-1605780274397-200ea3604d6fimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=32&w=32',
              medium:
                'https://images.unsplash.com/profile-1605780274397-200ea3604d6fimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=64&w=64',
              large:
                'https://images.unsplash.com/profile-1605780274397-200ea3604d6fimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=128&w=128',
            },
            instagram_username: 'heftiba.co.uk',
            total_collections: 38,
            total_likes: 2441,
            total_photos: 2706,
            accepted_tos: true,
            for_hire: true,
            social: {
              instagram_username: 'heftiba.co.uk',
              portfolio_url: 'http://heftiba.co.uk/',
              twitter_username: 'toaheftiba',
              paypal_email: null,
            },
          },
        },
        {
          id: 'KIM9ux3pgDY',
          created_at: '2022-01-16T06:47:06-05:00',
          updated_at: '2022-01-16T14:08:04-05:00',
          promoted_at: '2022-01-16T14:08:04-05:00',
          width: 3640,
          height: 5464,
          color: '#0c2626',
          blur_hash: 'LQB4F1WA8^azkCM_ogxvIUaxxuj]',
          description: null,
          alt_description: null,
          urls: {
            raw: 'https://images.unsplash.com/photo-1642333604604-5b7fd4787ed6?ixid=MnwyOTE2NzZ8MHwxfGFsbHw3fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1',
            full: 'https://images.unsplash.com/photo-1642333604604-5b7fd4787ed6?crop=entropy&cs=srgb&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw3fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=85',
            regular:
              'https://images.unsplash.com/photo-1642333604604-5b7fd4787ed6?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw3fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=1080',
            small:
              'https://images.unsplash.com/photo-1642333604604-5b7fd4787ed6?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw3fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=400',
            thumb:
              'https://images.unsplash.com/photo-1642333604604-5b7fd4787ed6?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw3fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=200',
          },
          links: {
            self: 'https://api.unsplash.com/photos/KIM9ux3pgDY',
            html: 'https://unsplash.com/photos/KIM9ux3pgDY',
            download:
              'https://unsplash.com/photos/KIM9ux3pgDY/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHw3fHx8fHx8Mnx8MTY0MjM2MjA0NQ',
            download_location:
              'https://api.unsplash.com/photos/KIM9ux3pgDY/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHw3fHx8fHx8Mnx8MTY0MjM2MjA0NQ',
          },
          categories: [],
          likes: 7,
          liked_by_user: false,
          current_user_collections: [],
          sponsorship: null,
          topic_submissions: {
            nature: {
              status: 'unevaluated',
            },
          },
          user: {
            id: 'Fj-_9Fg2Vjc',
            updated_at: '2022-01-16T14:37:25-05:00',
            username: 'maksym_tymchyk',
            name: 'Maksym Tymchyk',
            first_name: 'Maksym',
            last_name: 'Tymchyk',
            twitter_username: 'MaksymTymchyk',
            portfolio_url: 'https://foundation.app/@MaksymTymchyk',
            bio: "I'm Photographer and I'm Developer.\r\nEnjoy photography process and tell stories.",
            location: 'Kyiv, Ukraine',
            links: {
              self: 'https://api.unsplash.com/users/maksym_tymchyk',
              html: 'https://unsplash.com/@maksym_tymchyk',
              photos: 'https://api.unsplash.com/users/maksym_tymchyk/photos',
              likes: 'https://api.unsplash.com/users/maksym_tymchyk/likes',
              portfolio:
                'https://api.unsplash.com/users/maksym_tymchyk/portfolio',
              following:
                'https://api.unsplash.com/users/maksym_tymchyk/following',
              followers:
                'https://api.unsplash.com/users/maksym_tymchyk/followers',
            },
            profile_image: {
              small:
                'https://images.unsplash.com/profile-1625779039486-8c6960c88ee7image?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=32&w=32',
              medium:
                'https://images.unsplash.com/profile-1625779039486-8c6960c88ee7image?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=64&w=64',
              large:
                'https://images.unsplash.com/profile-1625779039486-8c6960c88ee7image?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=128&w=128',
            },
            instagram_username: 'maksym.tymchyk',
            total_collections: 31,
            total_likes: 2639,
            total_photos: 414,
            accepted_tos: true,
            for_hire: true,
            social: {
              instagram_username: 'maksym.tymchyk',
              portfolio_url: 'https://foundation.app/@MaksymTymchyk',
              twitter_username: 'MaksymTymchyk',
              paypal_email: null,
            },
          },
        },
        {
          id: 'JDMH4uvCDQU',
          created_at: '2022-01-16T07:01:18-05:00',
          updated_at: '2022-01-16T14:30:10-05:00',
          promoted_at: '2022-01-16T14:00:02-05:00',
          width: 3997,
          height: 4996,
          color: '#262626',
          blur_hash: 'L8En*h};9YRj=DV=S8t8JOEUJCNX',
          description:
            'A bee lands on a stem of lavender at Bridestowe Lavender Estate in Tasmania, Australia. ',
          alt_description: null,
          urls: {
            raw: 'https://images.unsplash.com/photo-1642333943436-ba4d647a677f?ixid=MnwyOTE2NzZ8MHwxfGFsbHw4fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1',
            full: 'https://images.unsplash.com/photo-1642333943436-ba4d647a677f?crop=entropy&cs=srgb&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw4fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=85',
            regular:
              'https://images.unsplash.com/photo-1642333943436-ba4d647a677f?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw4fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=1080',
            small:
              'https://images.unsplash.com/photo-1642333943436-ba4d647a677f?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw4fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=400',
            thumb:
              'https://images.unsplash.com/photo-1642333943436-ba4d647a677f?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw4fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=200',
          },
          links: {
            self: 'https://api.unsplash.com/photos/JDMH4uvCDQU',
            html: 'https://unsplash.com/photos/JDMH4uvCDQU',
            download:
              'https://unsplash.com/photos/JDMH4uvCDQU/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHw4fHx8fHx8Mnx8MTY0MjM2MjA0NQ',
            download_location:
              'https://api.unsplash.com/photos/JDMH4uvCDQU/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHw4fHx8fHx8Mnx8MTY0MjM2MjA0NQ',
          },
          categories: [],
          likes: 7,
          liked_by_user: false,
          current_user_collections: [],
          sponsorship: null,
          topic_submissions: {},
          user: {
            id: 'RR1KG-BqDl0',
            updated_at: '2022-01-16T14:32:26-05:00',
            username: 'lauraashtonashley',
            name: 'Laura Barry',
            first_name: 'Laura',
            last_name: 'Barry',
            twitter_username: null,
            portfolio_url: 'http://instagram.com/laura.ashton.b/',
            bio: 'A writer, digital content editor and photographer.\r\nFollow me on Insta @laura.ashton.b',
            location: 'NSW',
            links: {
              self: 'https://api.unsplash.com/users/lauraashtonashley',
              html: 'https://unsplash.com/@lauraashtonashley',
              photos: 'https://api.unsplash.com/users/lauraashtonashley/photos',
              likes: 'https://api.unsplash.com/users/lauraashtonashley/likes',
              portfolio:
                'https://api.unsplash.com/users/lauraashtonashley/portfolio',
              following:
                'https://api.unsplash.com/users/lauraashtonashley/following',
              followers:
                'https://api.unsplash.com/users/lauraashtonashley/followers',
            },
            profile_image: {
              small:
                'https://images.unsplash.com/profile-1620734130194-37f924d104baimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=32&w=32',
              medium:
                'https://images.unsplash.com/profile-1620734130194-37f924d104baimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=64&w=64',
              large:
                'https://images.unsplash.com/profile-1620734130194-37f924d104baimage?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=128&w=128',
            },
            instagram_username: 'Laura.ashton.b',
            total_collections: 0,
            total_likes: 111,
            total_photos: 151,
            accepted_tos: true,
            for_hire: true,
            social: {
              instagram_username: 'Laura.ashton.b',
              portfolio_url: 'http://instagram.com/laura.ashton.b/',
              twitter_username: null,
              paypal_email: null,
            },
          },
        },
        {
          id: 'efyBvqWuBh0',
          created_at: '2022-01-16T07:07:32-05:00',
          updated_at: '2022-01-16T13:56:01-05:00',
          promoted_at: '2022-01-16T13:56:01-05:00',
          width: 5304,
          height: 6905,
          color: '#404040',
          blur_hash: 'LaDcs@t6axRj_Mf6WBayNKWBWBof',
          description: 'Cloud dream',
          alt_description: null,
          urls: {
            raw: 'https://images.unsplash.com/photo-1642334806364-985d34e6bdf1?ixid=MnwyOTE2NzZ8MHwxfGFsbHw5fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1',
            full: 'https://images.unsplash.com/photo-1642334806364-985d34e6bdf1?crop=entropy&cs=srgb&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw5fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=85',
            regular:
              'https://images.unsplash.com/photo-1642334806364-985d34e6bdf1?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw5fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=1080',
            small:
              'https://images.unsplash.com/photo-1642334806364-985d34e6bdf1?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw5fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=400',
            thumb:
              'https://images.unsplash.com/photo-1642334806364-985d34e6bdf1?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHw5fHx8fHx8Mnx8MTY0MjM2MjA0NQ&ixlib=rb-1.2.1&q=80&w=200',
          },
          links: {
            self: 'https://api.unsplash.com/photos/efyBvqWuBh0',
            html: 'https://unsplash.com/photos/efyBvqWuBh0',
            download:
              'https://unsplash.com/photos/efyBvqWuBh0/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHw5fHx8fHx8Mnx8MTY0MjM2MjA0NQ',
            download_location:
              'https://api.unsplash.com/photos/efyBvqWuBh0/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHw5fHx8fHx8Mnx8MTY0MjM2MjA0NQ',
          },
          categories: [],
          likes: 7,
          liked_by_user: false,
          current_user_collections: [],
          sponsorship: null,
          topic_submissions: {},
          user: {
            id: 'wD8WJj91W8E',
            updated_at: '2022-01-16T14:32:26-05:00',
            username: 'finnnyc',
            name: 'Finn',
            first_name: 'Finn',
            last_name: null,
            twitter_username: null,
            portfolio_url: null,
            bio: 'Adelaide, South Australia\r\n If you use a pic please follow my gram @finnnyc  Prints available at @bowdenarthouse Instagram ',
            location: 'Adelaide, South Australia',
            links: {
              self: 'https://api.unsplash.com/users/finnnyc',
              html: 'https://unsplash.com/@finnnyc',
              photos: 'https://api.unsplash.com/users/finnnyc/photos',
              likes: 'https://api.unsplash.com/users/finnnyc/likes',
              portfolio: 'https://api.unsplash.com/users/finnnyc/portfolio',
              following: 'https://api.unsplash.com/users/finnnyc/following',
              followers: 'https://api.unsplash.com/users/finnnyc/followers',
            },
            profile_image: {
              small:
                'https://images.unsplash.com/profile-1556503618752-39ce49c9a9b6?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=32&w=32',
              medium:
                'https://images.unsplash.com/profile-1556503618752-39ce49c9a9b6?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=64&w=64',
              large:
                'https://images.unsplash.com/profile-1556503618752-39ce49c9a9b6?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=128&w=128',
            },
            instagram_username: 'finnnyc',
            total_collections: 0,
            total_likes: 15,
            total_photos: 78,
            accepted_tos: true,
            for_hire: true,
            social: {
              instagram_username: 'finnnyc',
              portfolio_url: null,
              twitter_username: null,
              paypal_email: null,
            },
          },
        },
        {
          id: 'dg8s_PsV2zw',
          created_at: '2022-01-15T17:40:36-05:00',
          updated_at: '2022-01-16T13:48:02-05:00',
          promoted_at: '2022-01-16T13:48:02-05:00',
          width: 2912,
          height: 4032,
          color: '#c0c0c0',
          blur_hash: 'LQCsp%_3M{oe00Rj%Nj]M_D%M{of',
          description: null,
          alt_description: null,
          urls: {
            raw: 'https://images.unsplash.com/photo-1642286423481-6fa838052bfb?ixid=MnwyOTE2NzZ8MHwxfGFsbHwxMHx8fHx8fDJ8fDE2NDIzNjIwNDU&ixlib=rb-1.2.1',
            full: 'https://images.unsplash.com/photo-1642286423481-6fa838052bfb?crop=entropy&cs=srgb&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHwxMHx8fHx8fDJ8fDE2NDIzNjIwNDU&ixlib=rb-1.2.1&q=85',
            regular:
              'https://images.unsplash.com/photo-1642286423481-6fa838052bfb?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHwxMHx8fHx8fDJ8fDE2NDIzNjIwNDU&ixlib=rb-1.2.1&q=80&w=1080',
            small:
              'https://images.unsplash.com/photo-1642286423481-6fa838052bfb?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHwxMHx8fHx8fDJ8fDE2NDIzNjIwNDU&ixlib=rb-1.2.1&q=80&w=400',
            thumb:
              'https://images.unsplash.com/photo-1642286423481-6fa838052bfb?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwyOTE2NzZ8MHwxfGFsbHwxMHx8fHx8fDJ8fDE2NDIzNjIwNDU&ixlib=rb-1.2.1&q=80&w=200',
          },
          links: {
            self: 'https://api.unsplash.com/photos/dg8s_PsV2zw',
            html: 'https://unsplash.com/photos/dg8s_PsV2zw',
            download:
              'https://unsplash.com/photos/dg8s_PsV2zw/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHwxMHx8fHx8fDJ8fDE2NDIzNjIwNDU',
            download_location:
              'https://api.unsplash.com/photos/dg8s_PsV2zw/download?ixid=MnwyOTE2NzZ8MHwxfGFsbHwxMHx8fHx8fDJ8fDE2NDIzNjIwNDU',
          },
          categories: [],
          likes: 13,
          liked_by_user: false,
          current_user_collections: [],
          sponsorship: null,
          topic_submissions: {},
          user: {
            id: 'NS9FujfPw5A',
            updated_at: '2022-01-16T14:23:40-05:00',
            username: 'idbronskiy',
            name: 'Ilya Bronskiy',
            first_name: 'Ilya',
            last_name: 'Bronskiy',
            twitter_username: 'IDBronskiy',
            portfolio_url: null,
            bio: null,
            location: 'Switzerland',
            links: {
              self: 'https://api.unsplash.com/users/idbronskiy',
              html: 'https://unsplash.com/@idbronskiy',
              photos: 'https://api.unsplash.com/users/idbronskiy/photos',
              likes: 'https://api.unsplash.com/users/idbronskiy/likes',
              portfolio: 'https://api.unsplash.com/users/idbronskiy/portfolio',
              following: 'https://api.unsplash.com/users/idbronskiy/following',
              followers: 'https://api.unsplash.com/users/idbronskiy/followers',
            },
            profile_image: {
              small:
                'https://images.unsplash.com/profile-1639074621611-14ea292eb437?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=32&w=32',
              medium:
                'https://images.unsplash.com/profile-1639074621611-14ea292eb437?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=64&w=64',
              large:
                'https://images.unsplash.com/profile-1639074621611-14ea292eb437?ixlib=rb-1.2.1&q=80&fm=jpg&crop=faces&cs=tinysrgb&fit=crop&h=128&w=128',
            },
            instagram_username: 'idbronskiy',
            total_collections: 0,
            total_likes: 21,
            total_photos: 141,
            accepted_tos: true,
            for_hire: true,
            social: {
              instagram_username: 'idbronskiy',
              portfolio_url: null,
              twitter_username: 'IDBronskiy',
              paypal_email: null,
            },
          },
        },
      ],
      columnCount: 3,
    }
  },
  async fetch() {
    console.log('fetching data')
    console.log(this.queryString)
    // await this.$axios
    //   .$get(this.queryString, {
    //     headers: {
    //       Authorization:
    //         'Client-ID kF3QiOFoeUB9phP9zmVo_aZoqZXwWvjtKEu6VJrva30',
    //     },
    //   })
    //   .then((response) => {
    //     this.serverPhotos = response.results || response
    //   })
    //   .catch((error) => {
    //     this.$nuxt.error({
    //       statusCode: error.response?.status || 404,
    //       message: 'Unable to fetch data from Unsplash',
    //     })
    //   })
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
            `${this.relativeSearchUrl}?per_page=15&query=${encodeURI(
              this.searchString
            )}`
          : // if not use the photos endpoint
            `${this.defaultRelativePath}?orientation=portrait&per_page=15`)
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

// main photo-container

.photo-main-container {
  display: grid;
  column-gap: 2rem;
  row-gap: 1rem;

  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  margin-top: -2.5rem;

  & > * {
    align-self: start;
    display: grid;
    row-gap: 1rem;
  }

  // for screens larger than 768px (tablet)
  // make allowance for inset paddings
  @media only screen and (min-width: 768px) {
    padding-right: 5%;
    padding-left: 5%;
  }
}
</style>
