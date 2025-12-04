<template>
  <header class="header">
    <div class="brand">Cooking Masterclass</div>
    <div class="right">
      <div class="wishlist">
        <svg viewBox="0 0 24 24" width="18" height="18" aria-hidden><path fill="currentColor" d="M12 21s-7-4.35-9.5-7.05C-0.5 9.7 4 5 7.5 7.5 9.14 8.9 12 11 12 11s2.86-2.1 4.5-3.5C20 5 24.5 9.7 21.5 13.95 19 16.65 12 21 12 21z"/></svg>
        <span class="count">{{ count }}</span>
      </div>
    </div>
  </header>
</template>

<script>
export default {
  name: 'HeaderBar',
  props: { count: { type: Number, required: true } }
}
</script>

<style scoped>
.header{
  display:flex;
  justify-content:space-between;
  align-items:center;
  padding:1rem 1.2rem;
  background:linear-gradient(90deg,#fff 0%, #fff 60%);
  border-bottom:1px solid rgba(0,0,0,0.06);
}
.brand{
  font-weight:700;
  font-size:1.15rem;
  color:var(--brand);
}
.wishlist{
  display:flex;
  align-items:center;
  gap:0.5rem;
  color:var(--accent);
  font-weight:600;
}
.count{
  background:rgba(255,122,0,0.12);
  padding:0.25rem 0.45rem;
  border-radius:12px;
}
</style>
