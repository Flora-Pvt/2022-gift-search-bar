<script lang="ts" setup>
import { ref, watch } from 'vue'

let searchTerm = ref('')
let matchingProducts = ref([])
let loading = ref(false)

const findProducts = async term => {
  const res = await fetch(`https://dummyjson.com/products/search?q=${term}`).catch(() => alert('An error has occured.'))
  const data = res ? await res.json() : null

  loading.value = false

  if (Array.isArray(data?.products)) {
    matchingProducts.value = data.products
  } else {
    alert('An error has occured.')
  }
}

const debounce = (func, timeout = 300) => {
  let timer

  clearTimeout(timer)
  timer = setTimeout(() => {
    func.apply(this)
  }, timeout)
}

watch(searchTerm, newTerm => {
  loading.value = true
  debounce(() => findProducts(newTerm))
})
</script>

<template>
  <div class="w-full pt-28 pb-40 flex flex-col gap-5 items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>

    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />

    <Transition>
      <div v-if="loading" class="flex justify-center items-center">
        <div class="lds-ring">
          <div></div>
          <div></div>
          <div></div>
          <div></div>
        </div>

        <p>loading data</p>
      </div>

      <ul v-else class="list-disc">
        <li v-if="matchingProducts.length && searchTerm.length" v-for="product in matchingProducts">
          {{ product.title }} - ${{ product.price }}
        </li>
        <li v-else class="list-none">{{ searchTerm.length ? 'no matching product' : '' }}</li>
      </ul>
    </Transition>
  </div>
</template>

<style>
.v-enter-active {
  transition: opacity 0.4s ease 0.4s;
}
.v-leave-active {
  transition: opacity 0.4s ease;
}
.v-enter-from,
.v-leave-to {
  opacity: 0;
}

.lds-ring {
  display: inline-block;
  position: relative;
  width: 1.25rem;
  height: 1.25rem;
  margin-right: 0.75rem;
}
.lds-ring div {
  box-sizing: border-box;
  display: block;
  position: absolute;
  width: 1.25rem;
  height: 1.25rem;
  border: 2px solid black;
  border-radius: 50%;
  animation: lds-ring 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
  border-color: black transparent transparent transparent;
}
.lds-ring div:nth-child(1) {
  animation-delay: -0.45s;
}
.lds-ring div:nth-child(2) {
  animation-delay: -0.3s;
}
.lds-ring div:nth-child(3) {
  animation-delay: -0.15s;
}
@keyframes lds-ring {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
