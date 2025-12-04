<template>
  <article class="card">
    <div class="image-wrapper">
      <img :src="course.image" :alt="course.title" />
      <span v-if="!course.available" class="sold-out">SOLD OUT</span>
    </div>

    <div class="info">
      <h3 class="title">{{ course.title }}</h3>
      <div class="meta">
        <span class="level">{{ course.level }}</span>
      </div>
      <p class="chef">{{ course.chef }}</p>

      <div class="bottom">
        <div class="price">{{ formattedPrice }}</div>
        <button
          class="add-btn"
          :disabled="!course.available"
          @click="$emit('add', course)"
        >
          {{ course.available ? 'Add' : 'Unavailable' }}
        </button>
      </div>
    </div>
  </article>
</template>

<script>
import { computed } from 'vue'
export default {
  name: 'CourseCard',
  props: { course: { type: Object, required: true } },
  setup(props){
    const formattedPrice = computed(() => {
      try {
        return new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' }).format(props.course.price)
      } catch { return `US$${props.course.price.toFixed(2)}` }
    })
    return { formattedPrice }
  }
}
</script>

<style scoped>
.card{
  background:var(--card);
  border-radius:12px;
  padding:0;
  overflow:hidden;
  display:flex;
  flex-direction:column;
  box-shadow:0 6px 20px rgba(15,23,42,0.06);
}
.image-wrapper{
  position:relative;
  height:150px;
  overflow:hidden;
}
.image-wrapper img{
  width:100%;
  height:100%;
  object-fit:cover;
  display:block;
}
.sold-out{
  position:absolute;
  top:10px;left:10px;
  background:#ff6b6b;
  color:white;
  padding:6px 10px;
  border-radius:6px;
  font-weight:700;
  font-size:0.8rem;
}
.info{
  padding:1rem;
  display:flex;
  flex-direction:column;
  gap:0.5rem;
}
.title{
  margin:0;
  font-size:1.05rem;
  color:var(--brand);
}
.meta{
  color:var(--muted);
  font-size:0.9rem;
}
.chef{
  margin:0;
  color:var(--muted);
}
.bottom{
  margin-top:auto;
  display:flex;
  align-items:center;
  justify-content:space-between;
  gap:0.8rem;
}
.price{
  font-weight:700;
  color:var(--accent);
}
.add-btn{
  background:var(--accent);
  color:white;
  border:none;
  padding:0.45rem 0.8rem;
  border-radius:8px;
  cursor:pointer;
}
.add-btn[disabled]{
  background:#e1e1e1;
  color:#888;
  cursor:not-allowed;
}
</style>
