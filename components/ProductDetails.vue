<template>
  <div class="product-main">
    <p class="product-charity">
      Support {{ product.vendor }} and
    </p>
    <product-title :title="product.title" />
    <div class="content">
      <product-description :description="description" />
    </div>
    <div class="how-it-works">
      <div class="how-it-works-step">
        <span class="icon icon-phone-money" />
        <span>Enter to win</span>
      </div>
      <div class="how-it-works-step">
        <span class="icon icon-tickets" />
        <span>A winner is drawn</span>
      </div>
      <div class="how-it-works-step">
        <span class="icon icon-globe" />
        <span>Donations benefit a great cause!</span>
      </div>
    </div>
    <button class="button is-primary" @click="scrollToEntries">
      enter now
    </button>
    <img class="charity-logo" :src="charityLogo" :alt="product.vendor" />
  </div>
</template>

<script>
import { mapState, mapMutations, mapGetters, mapActions } from 'vuex'
import { documentToHtmlString } from '@contentful/rich-text-html-renderer'
//import your own components here
//import ProductSpecial from '~/components/ComponentName'
export default {
  components: {
    //export your components by name here:
    // ComponentName
  },
  props: {
    product: {
      type: Object,
      default: () => {}
    }
  },
  watch: {
    variant(val) {
      if (val != null) {
        this.setVariant(this.variant)
      }
    }
  },
  computed: {
    currentVariant() {
      if (this.variant != null) {
        return this.variant
      } else {
        return this.product.variants[0]
      }
    },
    description () {
      if (
        this.product &&
        this.product.content &&
        this.product.content.fields &&
        this.product.content.fields.body
      ) {
        return documentToHtmlString(this.product.content.fields.body)
      }

      return ''
    },
    charityLogo () {
      if (
        this.product &&
        this.product.content &&
        this.product.content.fields &&
        this.product.content.fields.charityLogo
      ) {
        return this.product.content.fields.charityLogo.fields.file.url
      }

      return ''
    }
  },
  methods: {
    ...mapMutations('cart', ['showCart']),
    scrollToEntries () {
      const entries = document.querySelector('#product-entries')
      const rect = entries.getBoundingClientRect()

      window.scrollTo({ top: rect.top, behavior: "smooth" });
    }
  }
}
</script>

<style lang="scss" scoped>
.price {
  margin-bottom: 1rem;
}

.how-it-works {
  display: flex;
  margin-bottom: 1.875rem;
  padding: 0.8125rem 0.625rem;
  background-color: transparent;
  border: 1px solid #e2e2e1;
  border-radius: 0.3125rem;
}

.how-it-works-step {
  display: flex;
  align-items: center;
  flex-basis: 33%;

  .icon {
    flex-shrink: 0;
  }

  span:not(.icon) {
    padding-left: 0.3125rem;
    font-size: 0.75rem;
    font-weight: 600;
    letter-spacing: -.1px;
    line-height: 1.07;
  }
}

.charity-logo {
  display: block;
  margin: 1.25rem auto;
  max-width: 10rem;
  filter: grayscale(100%);
}
</style>
