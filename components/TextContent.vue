<template>
  <div class="text-content">
    <div v-html="content" class="content" />
  </div>
</template>

<script>
export default {
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
    content () {
      if (
        this.section &&
        this.section.data &&
        this.section.data.contentHtml
      ) {
        return this.section.data.contentHtml
      }

      return ''
    }
  }
}
</script>

<style>

</style>