<template>
  <div class="app">
    <HeaderBar :count="cartItemCount" />

    <main class="container">
      <section class="catalog">
        <h1>Available Courses</h1>
        <p class="sub">Browse classes and add them to your cart.</p>

        <div class="grid">
          <CourseCard
            v-for="course in courses"
            :key="course.id"
            :course="course"
            @add="addToCart"
          />
        </div>
      </section>

      <aside class="cart-panel">
        <CartPanel
          :items="cartItems"
          :tax-rate="taxRate"
          @updateQty="updateQty"
          @remove="removeFromCart"
          @clear="clearCart"
        />
      </aside>
    </main>

    <footer class="footer">Prototype — no payments, no login.</footer>
  </div>
</template>

<script>
import { ref, onMounted, watch } from 'vue'
import HeaderBar from './components/HeaderBar.vue'
import CourseCard from './components/CourseCard.vue'
import CartPanel from './components/CartPanel.vue'
import coursesData from './data/courses'

const STORAGE_KEY = 'cmc_cart_v1'

export default {
  name: 'App',
  components: { HeaderBar, CourseCard, CartPanel },
  setup(){
    const courses = ref(coursesData)
    const taxRate = ref(0.12) 
    const cartItems = ref([])

    onMounted(()=>{
      try {
        const raw = localStorage.getItem(STORAGE_KEY)
        if (raw) cartItems.value = JSON.parse(raw)
      } catch {}
    })

    watch(cartItems, (v) => {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(v))
    }, { deep: true })

    function findCartItem(id){
      return cartItems.value.find(i => i.id === id)
    }

    function addToCart(course){
      if (!course.available) return
      const existing = findCartItem(course.id)
      if (existing) {
        existing.qty += 1
      } else {
        cartItems.value.push({
          id: course.id,
          title: course.title,
          price: course.price,
          qty: 1
        })
      }
    }

    function updateQty({ id, qty }){
      const it = findCartItem(id)
      if (!it) return
      it.qty = Math.max(0, Math.floor(qty))
      if (it.qty === 0) {
        cartItems.value = cartItems.value.filter(x => x.id !== id)
      }
    }

    function removeFromCart(id){
      cartItems.value = cartItems.value.filter(x => x.id !== id)
    }

    function clearCart(){
      cartItems.value = []
    }

    const cartItemCount = ref(0)
    watch(cartItems, (v)=>{
      cartItemCount.value = v.reduce((s,i) => s + i.qty, 0)
    }, { immediate: true, deep: true })

    return { courses, addToCart, cartItems, updateQty, removeFromCart, clearCart, taxRate, cartItemCount }
  }
}
</script>

<style>
:root{
  --bg:#f6f8fa;
  --card:#fff;
  --muted:#6b7280;
  --accent:#ff7a00;
  --brand:#1f2937;
}
body{margin:0;font-family:Inter,system-ui,Arial;background:var(--bg);color:var(--brand)}
.app{min-height:100vh;display:flex;flex-direction:column}
.container{
  width:100%;
  max-width:1200px;
  margin:1.5rem auto;
  display:grid;
  grid-template-columns: 1fr 360px;
  gap:1.5rem;
  padding:0 1rem;
}
.catalog{background:transparent;}
.grid{
  display:grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  gap:1.5rem;
  margin-top:1rem;
}
.cart-panel{
  position:sticky;top:1rem;
}
.footer{text-align:center;color:var(--muted);padding:1.2rem 0;margin-top:auto}

@media (max-width:900px){
  .container{grid-template-columns: 1fr; padding-bottom:2rem}
  .cart-panel{position:static}
}
</style>
