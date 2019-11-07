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
import { fetchStatic } from '@nacelle/nacelle-tools'
import TrendingArticles from '@/components/TrendingArticles'
import ContestProductCard from '@/components/ContestProductCard'

export default {
  components: {
    TrendingArticles,
    ContestProductCard
  },
  data() {
    return {
      handle: this.$route.params.handle,
      article: null,
      collection: null
    }
  },
  async asyncData(context) {
    const { params } = context
    const { handle } = params
    const articleData = await fetchStatic.articleData(handle, context)
    const collectionData = await fetchStatic.collectionData(handle, context)
      
    return {
      ...articleData,
      ...collectionData
    }
  },
  computed: {
    ...mapGetters('space', ['getMetatag'])
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

    if (!this.article && !this.noArticleData) {
      this.$nacelleApollo.getArticle(
        this.handle,
        'blog',
        this.$apollo,
        {
          error: () => {
            this.$nacelleHelpers.debugLog('No article data.')
          }
        }
      )
    }
  },
  methods: {
    pageError () {
      this.$nuxt.error({ statusCode: 404, message: 'Article page does not exist' })
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

