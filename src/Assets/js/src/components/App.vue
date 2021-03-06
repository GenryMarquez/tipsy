<template>
  <div class="wrapper" :class="wrapperClasses">
    <div class="wrapper--bg" ref="wrapperBackground"></div>
    <app-header></app-header>
    <div class="content">
      <div class="verification-warning" v-if="notVerified">Your email address is not verified. Please check your inbox, or <router-link to="/request-verification">request a new verification mail</router-link>.</div>
      <transition name="fade" mode="out-in">
        <div class="app-pre-loader" v-if="loading">
          <div class="pre-load-spinner"></div>
        </div>
        <router-view v-if="!loading"></router-view>
      </transition>
    </div>
    <app-footer></app-footer>
    <notifications group="vote" width="400px"></notifications>
  </div>
</template>

<script>
  import { mapGetters } from 'vuex';

  import AppHeader from './Header.vue';
  import AppFooter from './Footer.vue';

  export default {
    name: 'app',

    components: {
      AppHeader,
      AppFooter
    },

    data() {
      return {
        loading: true
      }
    },

    computed: {
      ...mapGetters([
        'baseUrl',
        'icons',
        'notVerified'
      ]),
      wrapperClasses() {
        return [
          `route-${this.$route.name || 'none'}`
        ]
      }
    },

    methods: {
      getImagePromise(src) {
        return new Promise((resolve) => {
          const img = new Image();

          img.onload = () => {
            resolve();
          };
          img.src = src;
        });
      },

      preload() {
        const icons = this.icons;
        const backgroundImage = global.window.AppConfig.backgroundImage;
        const medalIcon3rd = global.window.AppConfig.medalIcon3rd;
        const trophyIcon = global.window.AppConfig.trophyIcon;
        const baseUrl = this.baseUrl;

        let promises = [];

        if (backgroundImage) {
          promises.push(this.getImagePromise(backgroundImage));
        }

        if (medalIcon3rd) {
          promises.push(this.getImagePromise(medalIcon3rd));
        }

        if (trophyIcon) {
          promises.push(this.getImagePromise(trophyIcon));
        }

        if (baseUrl) {
          icons.forEach(icon => {
            promises.push(this.getImagePromise(baseUrl + icon));
          });
        }

        return Promise.all(promises);
      }
    },

    mounted() {
      const backgroundImage = global.window.AppConfig.backgroundImage;

      this.preload().then(() => {
        this.loading = false;
        this.$refs.wrapperBackground.style.backgroundImage = 'url(' + backgroundImage +')';
        this.$nextTick(() => {
          this.$refs.wrapperBackground.classList.add('loaded');
        });
      });
    }
  };
</script>

<style lang="scss">
  @import '../../../../../node_modules/normalize.css/normalize.css';
  @import '../sass/imports';
  @import '../sass/icons';

  @import url('https://fonts.googleapis.com/css?family=Fira+Sans:400,600');

  *, *::before, *::after {
    box-sizing: border-box;
  }

  html, body, .wrapper {
    height: 100%;
    min-height: 100%;
  }

  body {
    font-family: $body-font-family;
    @include rem(font-size, $body-font-size);
    background-color: #efefef;
  }

  .wrapper--bg {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background: transparent no-repeat 50% 50%;
    background-size: cover;
    z-index: $zindex-bg-image;
    opacity: 0;
    will-change: opacity;
    transition: opacity 0.35s linear;

    .route-login &.loaded,
    .route-signup &.loaded,
    .route-signed-up &.loaded,
    .route-lost-password &.loaded,
    .route-lost-password-requested &.loaded,
    .route-reset-password &.loaded,
    .route-reset-password-complete &.loaded,
    .route-verify-email &.loaded,
    .route-request-verification &.loaded {
      opacity: 1;
    }
  }

  .content {
    position: relative;
    @include rem(padding-top, 44px);
    @include rem(padding-bottom, 59px);
    min-height: 100vh;
  }

  .verification-warning {
    @include rem(padding, 20px);
    color: #333;
    background-color: #ddd;
    text-align: center;

    .route-request-verification & {
      display: none;
    }
  }

  a {
    color: $link-color;
    text-decoration: none;

    &:hover {
      text-decoration: underline;
    }
  }

  .page-title {
    @include rem(margin, 30px 0 10px);
    @include rem(font-size, 20px);
    font-weight: 600;
    text-align: center;

    @include respond-to-min(400px) {
      @include rem(margin-bottom, 20px);
      @include rem(font-size, 28px);
    }
  }

  .page-title--divider {
    color: $rot;
  }

  .fade-enter {
    opacity: 0;
    pointer-events: none;
    user-select: none;
  }

  .fade-enter-active {
    pointer-events: none;
    user-select: none;
    transition: opacity .3s ease;
  }

  .fade-leave {
    pointer-events: none;
    user-select: none;
  }

  .fade-leave-active {
    pointer-events: none;
    user-select: none;
    opacity: 0;
  }

  .app-pre-loader {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .pre-load-spinner {
    @include rem(width, 100px);
    @include rem(height, 100px);
    animation: pre-loading-spinner-rotation .7s infinite linear;
    border: 10px solid rgba(0, 0, 0, 0.03);
    border-top-color: #d1d1d1;
    border-radius: 100%;
    transition: .3s all ease;
  }

  @keyframes pre-loading-spinner-rotation {
    from {
      transform: rotate(0deg)
    }
    to {
      transform: rotate(359deg)
    }
  }
</style>
