<template>
  <div id="product-entries" class="product-entries">
    <h2 class="title has-text-centered">entry levels</h2>
    <div class="product-entry-popular">
      <div class="entry">
        <div class="entry-flag">most popular!</div>
        <div class="entry-header">
          <h3 class="title">{{ popularEntry.title }} entries to win</h3>
        </div>
        <div class="entry-cta">
          <entry-donate-button :product="product" :variant="popularEntry" />
        </div>
      </div>
      <h4>
        Choose the amount below. No donation or payment is necessary to enter or win this sweepstakes.
      </h4>
    </div>
    <ul class="entries" v-if="product">
      <li 
        v-for="variant in product.variants"
        :key="variant.id"
        class="entry"
      >
        <div
          v-if="['250','1000','2000'].includes(variant.title)"
          class="entry-flag"
        >
          most popular!
        </div>
        <div class="entry-header">
          <h3 class="title">{{ variant.title }} entries to win</h3>
        </div>
        <div class="entry-body">
          <div class="entry-title">
            <span>{{ variant.title }}</span>
            <span>entries</span>
          </div>
          <p class="has-text-centered">
            <strong>{{ variant.title }} entries</strong><br>
            <small>{{ variant.title }} chances to {{ product.title }}</small>
          </p>
        </div>
        <div class="entry-cta">
          <entry-donate-button :product="product" :variant="variant" />
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import EntryDonateButton from '~/components/EntryDonateButton'

export default {
  components: {
    EntryDonateButton
  },
  props: {
    product: {
      type: Object,
      default: () => {},
      required: true
    }
  },
  computed: {
    popularEntry () {
      if (
        this.product &&
        this.product.variants &&
        this.product.variants.length > 0
      ) {
        const popular = this.product.variants.find(variant => {
          return variant.title === '250'
        })

        if (popular) {
          return popular
        }

        return this.product.variants[0]
      }

      return undefined
    }
  }
}
</script>

<style lang="scss" scoped>
.product-entries {
  margin-left: auto;
  margin-right: auto;
  max-width: 62.5rem;
}

.product-entry-popular {
  display: flex;
}

.entries {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  list-style-type: none;
}

.entry {
  position: relative;
  margin: 0 1% 0.9375rem;
  padding: 0;
  width: 31%;
  border: 1px solid #ededed;
  box-shadow: #ededed 1px 1px 3px -1px;
}

.entry-flag {
  position: absolute;
  top: 0;
  right: 1.25rem;
  color: #fff;
  line-height: 1;
  font-family: "National", sans-serif;
  font-style: italic;
  text-transform: none;
  padding: .3em .95em;
  background: #00caf2;
  border-radius: 2px;
  transform: translateY(-50%);
}

.entry-header {
  font-size: 0.9375rem;
  padding: 1.5625rem;
  text-align: center;

  .title {
    letter-spacing: -.6px;
    font-size: 1.375rem;
    font-weight: bold;
    font-style: normal;
    margin: 0;
  }
}

.entry-body {
  padding: 0 1.25rem;

  &:before {
    content: '';
    display: block;
    margin-bottom: 1.25rem;
    border-top: 1px solid #e2e2e1;
  }

  p {
    margin-bottom: 2rem;
  }
}

.entry-title {
  margin-bottom: 2rem;
  color: #00caf2;
  text-align: center;

  span {
    display: block;
    margin-left: auto;
    margin-right: auto;
    line-height: 1;
    font-weight: 700;

    &:first-child {
      font-size: 5rem;
    }

    &:last-child {
      font-size: 3rem;
    }
  }
}
</style>