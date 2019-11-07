<template>
  <div class="product">
    <section class="section product-body">
      <div class="container">
        <product-details v-if="product" :product="product" />
        <div class="product-content">
          <div v-if="product" class="time-remaining">
            {{ timeRemaining }}
          </div>
          <page-content 
            v-if="content"
            :page="content"
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
              <content-hero-banner
                v-if="section.contentType === 'ContentHeroBanner'"
                v-bind="section.data"
              />
              <content-side-by-side
                v-if="section.contentType === 'ContentSideBySide'"
                v-bind="section.data"
              />
              <content-testimonials
                v-if="section.contentType === 'ContentTestimonials'"
                v-bind="section.data"
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
import { fetchStatic } from '@nacelle/nacelle-tools'
import productMetafields from '@nacelle/nacelle-vue-components/dist/mixins/productMetafields'
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
      handle: this.$route.params.handle,
      product: null,
      content: null
    }
  },
  computed: {
    ...mapGetters('space', ['getMetatag']),
    legal () {
      if (
        this.content &&
        this.content.fields &&
        this.content.fields.legal
      ) {
        return documentToHtmlString(this.content.fields.legal)
      }

      return ''
    },
    endDate () {
      if (
        this.content &&
        this.content.fields &&
        this.content.fields.contestEndDate
      ) {
        return this.content.fields.contestEndDate
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
  async asyncData(context) {
    const { params } = context
    const { handle } = params
    const productData = await fetchStatic.productData(handle, context)
    const content = await context.app.$nacelle.content(handle, 'product')

    return {
      ...productData,
      content
    }
  },
  created() {
    if (!this.product && !this.noProductData) {
      this.$nacelleApollo.getProduct(
        this.handle,
        this.$apollo,
        {
          error: () => {
            this.$nacelleHelpers.debugLog('No product data.')
          }
        }
      )
    }
  },
  methods: {
    ...mapMutations('cart', ['showCart']),
    pageError () {
      this.$nuxt.error({ statusCode: 404, message: 'Product page does not exist' })
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
  }
}
</script>

<style lang="scss" scoped>
.product-body .container {
  @media screen and (min-width: 800px) {
    display: flex;
  }
}

.container .product-content {
  position: relative;
  
  @media screen and (min-width: 800px) {
    order: 0;
    width: 65%;
  }
}

.container /deep/ .product-main {
  @media screen and (min-width: 800px) {
    order: 1;
    margin-left: 3rem;
    width: 30%;
  }
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
