<template>
  <div class="product">
    <section class="section product-body">
      <div class="container">
        <product-details v-if="product" :product="product" />
        <div class="product-content">
          <div class="time-remaining">
            {{ timeRemaining }}
          </div>
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
      </div>
    </section>
    <section class="section product-details">
      <div class="container">
        <product-entries v-if="product" :product="product" />
      </div>
    </section>
    <section class="section product-legal">
      <div class="container">
        <div class="content" v-html="legal" />
      </div>
    </section>
  </div>
</template>

<script>
import { mapMutations, mapGetters } from 'vuex'
import { documentToHtmlString } from '@contentful/rich-text-html-renderer'
import formatDistance from 'date-fns/formatDistance'
import gql from 'graphql-tag'
import { getProduct } from '@nacelle/nacelle-graphql-queries-mixins'
import productMetafields from '@nacelle/nacelle-vue-components/dist/mixins/productMetafields'
import transformEdges from '~/plugins/utils/transformEdges.js'
import ProductDetails from '~/components/ProductDetails'
import CarouselContent from '~/components/CarouselContent'
import TextContent from '~/components/TextContent'
import ProductEntries from '~/components/ProductEntries'

export default {
  mixins: [productMetafields],
  components: {
    ProductDetails,
    CarouselContent,
    TextContent,
    ProductEntries
  },
  data() {
    return {
      product: null
    }
  },
  computed: {
    ...mapGetters('space', ['getMetatag']),
    legal () {
      if (
        this.product &&
        this.product.content &&
        this.product.content.fields &&
        this.product.content.fields.legal
      ) {
        return documentToHtmlString(this.product.content.fields.legal)
      }

      return ''
    },
    endDate () {
      if (
        this.product &&
        this.product.content &&
        this.product.content.fields &&
        this.product.content.fields.contestEndDate
      ) {
        return this.product.content.fields.contestEndDate
      }

      return Date.now()
    },
    timeRemaining () {
      return formatDistance(
        Date.now(),
        new Date(this.endDate)
      )
    }
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
.product-body .container {
  display: flex;
}

.container .product-content {
  position: relative;
  order: 0;
  width: 65%;
}

.container /deep/ .product-main {
  order: 1;
  margin-left: 3rem;
  width: 30%;
}

.time-remaining {
  position: absolute;
  top: 34px;
  right: 1.25rem;
  padding: 0.3em 1em;
  letter-spacing: 1px;
  line-height: normal;
  border-radius: 3px;
  background-color: #00caf2;
  color: #ffffff;
  font-size: 1rem;
  font-style: italic;
  z-index: 1;
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

.product-legal /deep/ h1 {
  text-align: center;
}
</style>
