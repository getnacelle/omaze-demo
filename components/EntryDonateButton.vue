<template>
  <button
    :disabled="variantInLineItems"
    @click="addToCart"
    class="button donate-button"
  >
    <span v-if="!variantInLineItems">donate ${{ totalPrice }}</span>
    <span v-else>added!</span>
  </button>
</template>

<script>
import { mapState, mapActions, mapMutations, mapGetters } from 'vuex'

export default {
  props: {
    product: {
      type: Object,
      required: true,
      default: () => {
        return {
          id: '',
          title: '',
          handle: '',
          featuredMedia: {}
        }
      }
    },
    variant: {
      type: Object,
      required: true,
      default: () => {
        return {
          id: '',
          title: '',
          price: 0
        }
      }
    }
  },
  computed: {
    ...mapState('cart', ['lineItems']),
    ...mapGetters('cart', ['checkoutLineItems']),
    variantInLineItems() {
      let vm = this
      if (vm.variant != null) {
        let lineItem = vm.lineItems.findIndex(lineItem => {
          return lineItem.variant.id == vm.variant.id
        })
        if (lineItem != -1) {
          return true
        } else {
          return false
        }
      } else {
        return false
      }
    },
    quantity () {
      if (this.variant && this.variant.title) {
        return parseInt(this.variant.title, 10)
      }

      return 0
    },
    priceNumber () {
      if (this.variant && this.variant.price) {
        return parseFloat(this.variant.price)
      }

      return 0
    },
    totalPrice () {
      return this.quantity * this.priceNumber
    }
  },
  methods: {
    ...mapActions('cart', [
      'addLineItem',
      'removeLineItem',
      'incrementLineItem',
      'decrementLineItem',
      'getLineItems'
    ]),
    ...mapMutations('cart', ['showCart']),
    addToCart() {
      const lineItem = {
        image: this.product.featuredMedia,
        title: this.product.title,
        variant: this.variant,
        quantity: this.quantity,
        productId: this.product.id,
        handle: this.product.handle
      }
      this.addLineItem(lineItem)
      this.showCart()
    }
  }
}
</script>

<style lang="scss" scoped>
.button {
  display: block;
  margin: 0.9375rem auto 1.25rem;
  font-size: 20px;
  font-weight: 700;
  padding: .75em .75em .55em .75em;
  min-width: 12.5rem;
  width: auto;
  height: auto;
  line-height: 1.15;
  background-color: #ea2786;
  color: #ffffff;
  border: 1px solid transparent;
  font-weight: 700;
  border-radius: 3px;
}
</style>