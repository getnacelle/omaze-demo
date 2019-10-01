<template>
  <div class="product">
    <section class="section">
      <div class="container">
        <product-details v-if="product" :product="product" />
        <page-content 
          v-if="product"
          :page="product.content"
          :products="[product]"
        >
          <template v-slot:section="{ section }">
            <carousel-content 
              v-if="section.contentType === 'ContentCarousel'"
              :section="section"
            />
            <text-content
              v-if="section.contentType === 'ContentText'"
              :section="section"
            />
          </template>
          <template v-slot:body>
            <div />
          </template>
        </page-content>
      </div>
    </section>
  </div>
</template>

<script>
import { mapMutations, mapGetters } from 'vuex'
import transformEdges from '~/plugins/utils/transformEdges.js'
import ProductDetails from '~/components/ProductDetails'
import { getProduct } from '@nacelle/nacelle-graphql-queries-mixins'
import productMetafields from '@nacelle/nacelle-vue-components/dist/mixins/productMetafields'
import gql from 'graphql-tag'
import CarouselContent from '~/components/CarouselContent'
import TextContent from '~/components/TextContent'

export default {
  mixins: [productMetafields],
  components: {
    ProductDetails,
    CarouselContent,
    TextContent
  },
  data() {
    return {
      product: null
    }
  },
  computed: {
    ...mapGetters('space', ['getMetatag'])
  },
  methods: {
    ...mapMutations('cart', ['showCart'])
  },

  async asyncData({ params, app, payload }) {
    if (payload) {
      const { variants, media, ...rest } = payload
      const transformedProduct = {
        variants: variants ? transformEdges(variants) : [],
        media: media ? transformEdges(media) : [],
        ...rest
      }
      return { product: transformedProduct }
    }
  },
  head() {
    if (this.product) {
      const properties = {}
      const meta = []
      const title = this.getMetatag('title')

      if (this.product.title) {
        let fullTitle = this.product.title

        if (title) {
          fullTitle = `${fullTitle} | ${title.value}`
        }

        properties.title = fullTitle
        meta.push({
          hid: 'og:title',
          property: 'og:title',
          content: fullTitle
        })
      }

      if (this.product.description) {
        meta.push({
          hid: 'description',
          name: 'description',
          content: this.product.description
        })
        meta.push({
          hid: 'og:description',
          property: 'og:description',
          content: this.product.description
        })
      }

      if (this.product.featuredMedia) {
        meta.push({
          hid: 'og:image',
          property: 'og:image',
          content: this.product.featuredMedia.src
        })
      }

      return {
        ...properties,
        meta
      }
    }
  },
  created() {
    if (process.browser) {
      getProduct({
        apollo: this.$apollo,
        params: this.$route.params,
        store: this.$store,
        nuxt: this.$nuxt,
        context: 'component'
      })
    }
  },
  mounted() {
    if (process.browser) {
      this.$apollo.queries.product.startPolling(5000)
    }
  }
}
</script>

<style lang="scss" scoped>
.container /deep/ .page-content > div:first-child,
.container /deep/ .page-content > div:nth-child(2) {
  width: 65%;
}

.price {
  margin-bottom: 1rem;
}

.product-meta .column {
  padding-bottom: 2rem;

  @media screen and (min-width: 769px) {
    padding-top: 3rem;
    padding-bottom: 0;
  }
}

.column.highlight {
  background-color: #f5f5f5;

  @media screen and (min-width: 769px) {
    padding: 3rem;
  }
}
</style>
