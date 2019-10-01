<template>
  <div>
    <vue-glide
      :type="'carousel'"
      :perView="1"
      :autoplay="true"
      :animation-duration="5000"
      ref="slider"
    >
      <vue-glide-slide
        v-for="(slide, index) in slides"
        :key="index"
      >
        <div 
          :class="slideClass(slide)"
          class="carousel-slide"
        >
          <figure>
            <img :src="slideBackgroundImage(slide)" alt="" />
          </figure>
        </div>
      </vue-glide-slide>
    </vue-glide>
  </div>
</template>

<script>
import 'vue-glide-js/dist/vue-glide.css'
import { Glide, GlideSlide } from 'vue-glide-js'

export default {
  components: {
    [Glide.name]: Glide,
    [GlideSlide.name]: GlideSlide,
  },
  props: {
    section: {
      type: Object,
      default: () => {
        return {
          handle: '',
          contentType: '',
          data: {}
        }
      }
    }
  },
  computed: {
    slides () {
      if (this.section && this.section.data && this.section.data.slides) {
        return this.section.data.slides
      }

      return []
    }
  },
  mounted () {
    this.$refs.slider.glide.update()
  },
  methods: {
    slideClass (slide) {
      if (slide && slide.fields && slide.fields.slideType) {
        const handle = slide.fields.slideType
          .toLowerCase()
          .replace(/[^a-z0-9]+/g, '-')
          .replace(/-$/, '')
          .replace(/^-/, '')

        return `is-${handle}`
      }

      return 'is-default-slide'
    },
    slideBackgroundImage (slide) {
      if (
        slide &&
        slide.fields &&
        slide.fields.backgroundMedia &&
        slide.fields.backgroundMedia.fields &&
        slide.fields.backgroundMedia.fields.file &&
        slide.fields.backgroundMedia.fields.file.url
      ) {
        return slide.fields.backgroundMedia.fields.file.url
      }

      return ''
    }
  }
}
</script>

<style lang="scss" scoped>
.carousel-slide {
  max-width: 100%;
}
</style>
