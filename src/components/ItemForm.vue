<template>
  <div class="columns">
    <div class="column is-3">
      <div class="field">
        <label>商品名</label>
        <div class="control">
          <input type="text" class="input" v-model="item.title" />
        </div>
      </div>
    </div>

    <div class="column is-3">
      <div class="field">
        <label>価格</label>
        <div class="control">
          <input type="text" class="input" v-model="item.unit_price" />
        </div>
      </div>
    </div>

    <div class="column is-2">
      <div class="field">
        <label>数量</label>
        <div class="control">
          <input type="number" class="input" v-model="item.quantity" />
        </div>
      </div>
    </div>

    <div class="column is-2">
      <div class="field">
        <label>消費税</label>
        <div class="control">
          <div class="select">
            <select v-model="item.vat_rate">
              <option value="10">10%</option>
              <option value="0">0%</option>
            </select>
          </div>
        </div>
      </div>
    </div>

    <div class="column is-2">
      <div class="field">
        <label>合計</label>
        <div class="control">
          <input
            type="text"
            class="input"
            v-bind:value="gross_amount"
            disabled
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ItemForm",
  props: {
      initialItem: Object,
  },
  data() {
      return {
          item: this.initialItem
      }
  },
  computed: {
      gross_amount() {
          const unit_price = this.item.unit_price
          const quantity = this.item.quantity
          const vat_rate = this.item.vat_rate

          this.item.net_amount = unit_price * quantity

          this.$emit('updatePrice', this.item)

          return this.item.net_amount + (this.item.net_amount * (vat_rate / 100))
      }
  }
};
</script>

<style>
</style>