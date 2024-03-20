<script setup>
     defineProps({
        id: Number,
        title: String,
        imageUrl: String,
        price: Number,
        isFavorite: Boolean,
        isAdded: Boolean,
        onClickFavorite: Function,
        onClickAdd: Function
    })

</script>

<template>
    <div>
      <div class="relative bg-white border border-slate-100 rounded-3xl p-10 cursor-pointer transition hover:-translate-y-5 hover:shadow-xl">
        <img 
          v-if="onClickFavorite"
          @click="onClickFavorite" 
          :src="!isFavorite ? '/like-1.svg' : '/like-2.svg'" 
          alt="Like 1" 
          class="absolute top-8 left-8">

        <img :src="imageUrl" alt="sneakers 1">
        <p class="mt-2">{{title}}</p>
        <div class="flex justify-between mt-5">
          <div class="flex flex-col">
            <span class="text-slate-400">Цена</span>
            <b>{{price}}</b>
          </div>
          <img           
            v-if="onClickAdd"
            @click="onClickAdd" 
            :src="!isAdded ? '/plus.svg' : '/checked.svg'" alt="Plus">
        </div>
      </div>
    </div>
</template>