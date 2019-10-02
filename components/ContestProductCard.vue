<template>
  <div class="contest-product-card">
    <router-link :to="`${pathFragment}${product.handle}`">
      <product-image :source="mediaSrc" ref="product-image" />
    </router-link>
    <div class="product-card-details">
      <router-link :to="`${pathFragment}${product.handle}`">
        <product-title :title="product.title" />
      </router-link>
    </div>
    <div class="product-card-actions">
      <label>Select Entry Level</label>
      <div class="select">
        <select v-model="selectedVariant">
          <option
            v-for="variant in product.variants"
            :key="variant.id"
            :value="variant"
          >
            {{ variant.title }}
          </option>
        </select>
      </div>
      <p class="has-text-centered">
        <strong>{{ currentVariant.title }} entries</strong><br>
        <small>{{ currentVariant.title }} chances to {{ product.title }}</small>
      </p>
      <div class="product-card-atc">
        <entry-donate-button :product="product" :variant="currentVariant" />
      </div>
    </div>
  </div>
</template>

<script>
import EntryDonateButton from '~/components/EntryDonateButton'

export default {
  components: {
    EntryDonateButton
  },
  props: {
    pathFragment: {
      type: String,
      default: '/products/'
    },
    product: {
      type: Object,
      required: true,
      default: () => {
        return {
          priceRange: {
            min: '0.0',
            max: '0.00'
          },
          title: null,
          featuredMedia: {
            src: undefined
          },
          id: null,
          handle: '',
          variants: []
        }
      }
    }
  },
  data () {
    return {
      selectedVariant: null
    }
  },
  computed: {
    currentVariant() {
      if (this.selectedVariant) {
        return this.selectedVariant
      }

      if (
        this.product &&
        this.product.variants &&
        this.product.variants.length
      ) {
        return this.product.variants[0]
      }

      return undefined
    },
    mediaSrc() {
      if (
        this.product &&
        this.product.featuredMedia &&
        this.product.featuredMedia.src
      ) {
        return this.product.featuredMedia.src
      }

      return undefined
    }
  },
  mounted () {
    if (
      this.product &&
      this.product.variants &&
      this.product.variants.length
    ) {
      this.selectedVariant = this.product.variants[0]
    }
  }
}
</script>

<style lang="scss" scoped>
.product-card-actions label {
  display: block;
}

.select,
.select select {
  display: block;
  width: 100%;
}

.select {
  margin-bottom: 1rem;
}
</style>