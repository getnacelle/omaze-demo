<template>
  <div class="site-footer">
    <div class="container">
      <div class="footer-social has-text-centered">
        <h3 class="title">follow us on:</h3>
        <div class="social-icons">
          <a :href="socialUrls.youtube">
            <span class="icon-youtube" />
            <span class="is-sr-only">YouTube</span>
          </a>
          <a :href="socialUrls.facebook">
            <span class="icon-facebook" />
            <span class="is-sr-only">Facebook</span>
          </a>
          <a :href="socialUrls.twitter">
            <span class="icon-twitter" />
            <span class="is-sr-only">Twitter</span>
          </a>
          <a :href="socialUrls.instagram">
            <span class="icon-instagram" />
            <span class="is-sr-only">Instagram</span>
          </a>
        </div>
      </div>
      <p class="footer-contact has-text-centered">
        Questions? Contact <a href="mailto:weloveyou@omaze.com">weloveyou@omaze.com</a>
      </p>
      <div class="site-footer-menu">
        <ul class="footer-menu">
          <li v-for="(link, index) in footerMenu" :key="index">
            <nuxt-link :to="link.to">{{ link.title }}</nuxt-link>
          </li>
        </ul>
      </div>
      <div class="site-footer-end columns">
        <div class="column is-one-quarter">
          <div class="icon-logo">
            <span class="is-sr-only">Omaze</span>
          </div>
          <p>
            <small>
              Copyright © 2019, Omaze, Inc. All Rights Reserved.
            </small>
          </p>
        </div>
        <div class="column">
          <ul class="legal-menu">
            <li v-for="(link, index) in legalMenu" :key="index">
              <nuxt-link :to="link.to">{{ link.title }}</nuxt-link>
            </li>
          </ul>
        </div>
        <div class="column is-one-quarter">
          <img src="/bbb-accreditation-logo@3x.png" alt="BBB Acredited Business Badge" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex'

export default {
  computed: {
    ...mapState('space', ['id', 'name', 'linklists']),
    ...mapGetters('space', ['getLinks', 'getMetaNamespace']),
    logoSrc() {
      if (this.id) {
        return `https://nacelle-assets.s3-us-west-2.amazonaws.com/space/${this.id}/logo.png`
      }

      return ''
    },
    footerMenu() {
      return this.getLinks('footer-menu')
    },
    legalMenu() {
      return this.getLinks('footer-menu-legal')
    },
    socialUrls() {
      return this.getMetaNamespace('social')
    }
  }
}
</script>

<style lang="scss" scoped>
.site-footer {
  padding-top: 2rem;
  background-color: #1a1a1a;
  color: #ffffff;
}

.logo {
  display: flex;
  justify-content: center;

  @media screen and (max-width: 786px) {
    align-content: center;
  }
}

img {
  max-width: 50%;
  align-self: center;
}

.title {
  margin-bottom: 0.5em;
}

a {
  color: #ffffff;
  letter-spacing: 0.016875rem;
}

.footer-social .title {
  color: #00caf2;
}

.social-icons {
  display: flex;
  align-items: center;
  justify-content: center;
}

.social-icons a {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 0.25rem;
  background-color: #00caf2;
  width: 2.25rem;
  height: 2.25rem;
  border-radius: 50%;
}

.footer-contact {
  font-size: 1rem;
  padding: 2.25rem 0 2.5625rem;

  a {
    &:hover,
    &:focus {
      text-decoration: underline;
    }
  }
}

.footer-menu {
  display: flex;
  justify-content: center;
  padding-bottom: 1.625rem;
  border-bottom: 0.0625rem solid #fff;
}

.footer-menu li {
  flex-grow: 1;
  margin: 0 0.5625rem;
  padding-bottom: 0;

  a {
    color: #fff;
    display: inline-block;
    line-height: 1;
    min-width: 100%;
    padding: 0.75rem 0.625rem 0.5rem;
    text-align: center;
    transition: all 0.3s ease;
    border: 1px solid rgba(255,255,255,0.65);
    border-radius: 3px;
    font-size: 0.875em;
    text-transform: lowercase;

    &:hover,
    &:focus {
      background-color: #ffffff;
      color: #000000;
    }
  }
}

.legal-menu {
  margin: 0.875rem 0 1.25rem;
  text-align: center;
}

.legal-menu li {
  display: inline-block;
  padding: 1rem;
  padding-bottom: 1.0625rem;

  a {
    font-size: 0.875rem;
  }
}

.site-footer-end {
  padding-top: 0.75rem;

  small {
    font-size: 0.5625rem;
  }
}
</style>
