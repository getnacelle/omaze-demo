<template>
  <div class="page page-shop" v-if="collection">
    <content-hero-banner
      v-if="collection"
      :title="collection.title"
      :backgroundImgUrl="featuredImage"
    />
    <section class="section">
      <div class="container">
        <div class="columns is-multiline">
          <product-grid
            v-if="products"
            :products="products"
            :showAddToCart="true"
            :showQuantityUpdate="true"
          />
        </div>
      </div>
      <div ref="fetchMore" class="fetch-more-component"></div>
    </section>
  </div>
</template>

<script>
import axios from 'axios'
import { mapState, mapGetters, mapMutations } from 'vuex'
import ProductGrid from '~/components/ProductGrid'
import { fetchStatic } from '@nacelle/nacelle-tools'

export default {
  name: 'home',
  components: { ProductGrid },
  data() {
    return {
      handle: this.$route.params.handle,
      collection: null
    }
  },
  async asyncData(context) {
    const { params } = context
    const { handle } = params
    const collectionData = await fetchStatic.collectionData(handle, context)
      
    return {
      ...collectionData
    }
  },
  computed: {
    ...mapGetters('space', ['getMetatag']),
    products() {
      if (
        this.collection &&
        this.collection.products &&
        this.collection.products.length
      ) {
        return this.collection.products
      }

      return null
    },
    featuredImage() {
      if (
        this.collection &&
        this.collection.featuredMedia &&
        this.collection.featuredMedia.src
      ) {
        return this.collection.featuredMedia.src
      }

      return null
    }
  },
  created () {
    if (!this.collection && !this.noCollectionData) {
      this.$nacelleApollo.getCollection(
        this.handle,
        this.$apollo,
        {
          error: () => {
            this.$nacelleHelpers.debugLog('No collection data.')
          }
        }
      )
    }
  },
  methods: {
    pageError () {
      this.$nuxt.error({ statusCode: 404, message: 'Page does not exist' })
    }
  },
  head() {
    if (this.collection) {
      const properties = {}
      const meta = []
      const title = this.getMetatag('title')

      if (this.collection.title) {
        let fullTitle = this.collection.title

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

      if (this.collection.description) {
        meta.push({
          hid: 'description',
          name: 'description',
          content: this.collection.description
        })
        meta.push({
          hid: 'og:description',
          property: 'og:description',
          content: this.collection.description
        })
      }

      if (this.featuredImage) {
        meta.push({
          hid: 'og:image',
          property: 'og:image',
          content: this.featuredImage
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
// .products {
//   display: flex;
//   flex-wrap: wrap;
// }
.product {
  // width: 20rem;
  // height: 20rem;
  // text-decoration: none;
  // color: black;
  // display: flex;
  // flex-direction: column;
  // margin-bottom: 2rem;
  // flex-grow: 1;
  // justify-content: center;
  // align-items: center;
  .title {
    font-weight: bold;
  }
  img {
    width: 250px;
  }
}
</style>