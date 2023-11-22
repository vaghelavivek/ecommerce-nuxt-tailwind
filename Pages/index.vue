<script setup>
const productDetails = ref({
    brand: 'Samsung',
    inStock: true,
    name: 'samsung galaxy s23 plus 5G',
    price: 880,
    details: {
        sim: 'Dual Sim',
        condition: 'Like New',
        color: 'Lavender',
        network: 'unlocked',
        storage: '512GB'
    },
    thumbnails : [ 
        {
            img : '_nuxt/assets/images/product-image.jpeg'
        },
        {
            img : '_nuxt/assets/images/product-image-front-large.jpeg'
        },
        {
            img : '_nuxt/assets/images/product-image-back.jpeg'
        },
        {
            video : "_nuxt/assets/videos/single-click-zoom-desktop.mp4"
        },
    ]
})

const isModelOpen = ref(false);
const currentOpenImageIndex = ref(0);
const currentOpenModelType = ref(1);

const isLansShow = ref(false)

</script>

<template>
    <Modal v-show="isModelOpen" :index="currentOpenImageIndex" :details="productDetails" :type="currentOpenModelType" @closeModel="isModelOpen = false , isLansShow = false" />
    <div class="h-full xl:grid grid-cols-2 xl:py-16 py-8" v-show="!isModelOpen">
        <div class="xl:pr-32 px-[16px]">
            <div class="h-full">
                <Carousel :thumbnails="productDetails.thumbnails" @lensShow="(val) => isLansShow = val" @openModal="(index , type) => { isModelOpen = true , currentOpenImageIndex = index, currentOpenModelType = type }" />
            </div>
        </div>
        <div class="xl:pl-10 px-[16px] xl:pt-0 pt-8">
            <div class="h-full">
                <div class="h-full bg-no-repeat" id="resultArea" :class="isLansShow ? 'xl:block hidden' : 'hidden'" ></div>
                <ProductDetails :data="productDetails" :class="isLansShow ? 'xl:hidden' : ''" />
            </div>
        </div>
    </div>
</template>
