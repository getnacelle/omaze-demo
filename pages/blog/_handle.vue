<template>
  <div class="article-page">
    <div class="container">
      <div class="columns">
        <article class="article column is-8">
          <transition name="fadeDelay">
            <div v-if="article">
              <blog-article-header
                  :title="article.title"
                  :author="article.author"
                  :tags="article.tags"
                  :publishDate="article.publishDate"
                >
                <template v-slot:date="{ date }">
                  <p class="article-published">
                    {{ date }} | 7 Comments
                  </p>
                </template>
                <interface-featured-media
                  v-if="article && article.featuredMedia"
                  :media="article.featuredMedia"
                />
              </blog-article-header>
              <div class="content">
                <blog-article-content
                  :article="article"
                  :products="collection ? collection.products : []"
                  shopLookButtonText="Enter this contest!"
                >
                  <!-- Extra HTML added after content -->
                  <nuxt-link :to="'/blog'" class="breadcrumb">Back to Blog</nuxt-link>

                  <template v-slot:product-card="{ product }">
                    <contest-product-card :product="product" />
                  </template>
                </blog-article-content>
              </div>
            </div>
          </transition>
        </article>
        <div class="column is-3 is-offset-1">
          <trending-articles />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import { getBlogArticle } from '@nacelle/nacelle-graphql-queries-mixins'
import ContestProductCard from '~/components/ContestProductCard'
import TrendingArticles from '~/components/TrendingArticles'

export default {
  components: {
    ContestProductCard,
    TrendingArticles
  },
  data() {
    return {
      article: null,
      collection: null
    }
  },
  computed: {
    ...mapGetters('space', ['getMetatag'])
  },
  async asyncData({ params, app, payload }) {
    if (payload) {
      return { article: payload }
    }
  },
  created() {
    if (process.browser) {
      getBlogArticle({
        apollo: this.$apollo,
        params: this.$route.params
      })
    }
  },
  mounted() {
    if (JSON.stringify(this.article) == '{}') {
      this.$nuxt.error({
        statusCode: 404,
        message: 'That article could not be found'
      })
    }
  },

  head() {
    if (this.article) {
      const properties = {}
      const meta = []
      const title = this.getMetatag('title')

      if (this.article.title) {
        let fullTitle = this.article.title

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

      if (this.article.description) {
        meta.push({
          hid: 'description',
          name: 'description',
          content: this.article.description
        })
        meta.push({
          hid: 'og:description',
          property: 'og:description',
          content: this.article.description
        })
      }

      if (this.article.featuredMedia) {
        meta.push({
          hid: 'og:image',
          property: 'og:image',
          content: this.article.featuredMedia.src
        })
      }

      meta.push({
        hid: 'og:type',
        property: 'og:type',
        content: 'article'
      })

      return {
        ...properties,
        meta
      }
    }
  }
}
</script>

<style lang="scss" scoped>
@import '~assets/scss/variables';

.article-page {
  padding-top: 2rem;
  padding-bottom: 2rem;
}

.article-title {
  font-family: ClanOT,sans-serif;
  font-size: 2.25rem;
  font-weight: 900;
  font-style: normal;
  color: inherit;
  text-rendering: optimizeLegibility;
  margin-top: 0;
  margin-bottom: 8px;
  margin-bottom: .5rem;
  line-height: 1.2;
}

.article-published {
  margin-bottom: 2rem;
  font-size: 1.5rem;
  color: $gray;
  text-transform: uppercase;
}

.article-tags {
  margin-bottom: 1em;
}

.article /deep/ .featured-media.nacelle {
  margin-bottom: 2rem;
}

.article-body .content {
  position: relative;
}

.fade-enter-active {
  transition: opacity 0.25s;
}
.fade-enter {
  opacity: 0;
}

.fadeDelay-enter-active {
  transition: opacity 0.55s 0.25s;
}
.fadeDelay-enter {
  opacity: 0;
}
</style>

