<script setup>
import { ref } from 'vue';

const props = defineProps({
    data : {
        required : true,
    }
})

const counter = ref(1)
</script>

<template>
    <div>
        <!-- details top -->
        <div class="flex items-center justify-between">
            <!-- brand -->
            <p class="md:text-[18px] text-base font-normal text-slate-400 uppercase" >{{ data.brand }}</p>
            <!-- stock -->
            <p class="text-green-700 text-lg" >{{ data.inStock ?  "Hurry Limited Stock" : "Out Of Stock"}}</p>
        </div>

        <!-- product name -->
        <h1 class="md:text-[32px] text-[24px] font-semibold my-2 capitalize" >{{ data.name }}</h1>
        
        <!-- product price -->
        <p class="md:text-[24px] text-[20px] font-bold" >£{{ data.price.toFixed(2) }}</p>
        
        <hr class="my-4 border-2 border-[#e0e0e0] text-[#e0e0e0]" >
        
        <!-- product details -->
        <p class="md:text-lg text-base py-2" v-for="(details , index) in Object.entries(data.details)" >
            <strong class="capitalize">{{ details[0] }} : </strong>
            <span class="uppercase tracking-wide" > {{ details[1] }} </span>
        </p>
        
        <hr class="my-4 border border-[#e0e0e0] text-[#e0e0e0]" >

        <!-- counter -->
        <div class="p-1 w-fit mb-4">
            <button class="pb-1 px-3 text-3xl" @click="counter == 1 ? counter = 1 : counter--" >-</button>
            <span class="py-2 px-4 mx-2 border border-[#eeeeee] rounded bg-[#eeeeee]" >{{ counter }}</span>
            <button class="pb-1 px-3 text-3xl" @click="counter++" >+</button>
        </div>

        <!-- add to cart btn -->
        <button class="bg-yellow-400 w-full py-2 rounded font-bold text-xl capitalize" >Add To Basket</button>
    </div>
</template>