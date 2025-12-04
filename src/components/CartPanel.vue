<template>
  <div class="cart">
    <h3>Your Cart</h3>

    <div v-if="items.length === 0" class="empty">Your cart is empty.</div>

    <div v-else>
      <div class="line" v-for="it in items" :key="it.id">
        <div class="left">
          <div class="title">{{ it.title }}</div>
          <div class="meta">Unit: {{ formatCurrency(it.price) }}</div>
        </div>

        <div class="right">
          <input type="number" min="0" step="1" class="qty" :value="it.qty" @input="e => onQty(it.id, e.target.value)" />
          <div class="line-total">{{ formatCurrency(it.price * it.qty) }}</div>
          <button class="remove" @click="$emit('remove', it.id)">✕</button>
        </div>
      </div>

      <div class="summary">
        <div class="row"><span>Subtotal</span><strong>{{ formatCurrency(subtotal) }}</strong></div>
        <div class="row"><span>Tax ({{ (taxRate*100).toFixed(0) }}%)</span><strong>{{ formatCurrency(tax) }}</strong></div>
        <div class="row total"><span>Grand Total</span><strong>{{ formatCurrency(grandTotal) }}</strong></div>
      </div>

      <div class="cart-actions">
        <button class="clear" @click="$emit('clear')">Clear Cart</button>
        <button class="checkout" @click="checkout">Proceed to Checkout</button>
      </div>
    </div>
  </div>
</template>

<script>
import { computed } from 'vue'
export default {
  name:'CartPanel',
  props: {
    items: { type: Array, default: () => [] },
    taxRate: { type: Number, default: 0.12 }
  },
  emits: ['updateQty','remove','clear'],
  setup(props, { emit }){
    const subtotal = computed(() => props.items.reduce((s,i) => s + (i.price * i.qty), 0))
    const tax = computed(() => subtotal.value * props.taxRate)
    const grandTotal = computed(() => subtotal.value + tax.value)

    function formatCurrency(v){
      try {
        return new Intl.NumberFormat('en-US',{ style:'currency', currency:'USD'}).format(v)
      } catch { return `US$${v.toFixed(2)}` }
    }

    function onQty(id, raw){
      const val = parseInt(raw || 0, 10)
      emit('updateQty', { id, qty: isNaN(val) ? 0 : val })
    }

    function checkout(){
      alert('This is a prototype: checkout simulated. Grand total: ' + formatCurrency(grandTotal.value))
    }

    return { subtotal, tax, grandTotal, formatCurrency, onQty, checkout }
  }
}
</script>

<style scoped>
.cart{
  background:var(--card);
  padding:1rem;
  border-radius:12px;
  box-shadow:0 6px 20px rgba(15,23,42,0.06);
}
.empty{
  padding:1rem;
  color:var(--muted);
}
.line{
  display:flex;
  justify-content:space-between;
  align-items:center;
  padding:0.6rem 0;
  border-bottom:1px dashed #eee;
}
.left .title{
  font-weight:700;
}
.left .meta{
  font-size:0.85rem;
  color:var(--muted);
}
.right{
  display:flex;
  align-items:center;
  gap:0.6rem;
}
.qty{
  width:64px;
  padding:6px;
  border-radius:6px;
  border:1px solid #ddd;
}
.line-total{
  font-weight:700;
}
.remove{
  background:transparent;
  border:none;
  color:#c0392b;
  font-weight:700;
  cursor:pointer;
}
.summary{
  margin-top:1rem;
  border-top:1px solid #f0f0f0;
  padding-top:1rem;
}
.row{
  display:flex;
  justify-content:space-between;
  padding:0.25rem 0;
}
.total{
  font-size:1.05rem;
}
.cart-actions{
  display:flex;
  gap:0.6rem;
  margin-top:1rem;
  justify-content:flex-end;
}
.clear{
  background:#f0f0f0;
  border:none;
  padding:0.6rem 0.8rem;
  border-radius:8px;
  cursor:pointer;
}
.checkout{
  background:var(--accent);
  color:white;
  border:none;
  padding:0.6rem 1rem;
  border-radius:8px;
  cursor:pointer;
}
</style>
