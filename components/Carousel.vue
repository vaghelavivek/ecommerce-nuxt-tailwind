<script setup>

const props = defineProps({
    thumbnails: {
        required: true
    },
})

const startX = ref(0)
const isSwiping = ref(false)
const currentIndex = ref(0)

const slideArr = ref([...props.thumbnails])

const slides = ref(null)
const wrapper = ref(null)
const dots = ref(null)

onMounted(() => {
    slides.value = document.querySelectorAll('.slides');
    dots.value = document.querySelectorAll('.pagi-dot');

    result.value = document.getElementById('resultArea');

    windowObj.value = window
})

const handleTouchStart = (e) => {
    isSwiping.value = true;
    startX.value = e.touches ? e.touches[0].clientX : e.clientX;
}

const setActivePaginationDot = () => {
    dots.value.forEach((dot, index) => {
        dot.classList.toggle('active', index === currentIndex);
    })
}


const updateSlider = () => {
    const slideWidth = ref(slides.value[0].offsetWidth)
    wrapper.value.style.transform = `translateX(${-currentIndex.value * slideWidth.value}px)`
}

const prevSlide = () => {
    currentIndex.value = (currentIndex.value - 1 + slideArr.value.length) % slideArr.value.length
    updateSlider()
    setActivePaginationDot()
}

const nextSlide = () => {
    currentIndex.value = (currentIndex.value + 1) % slideArr.value.length
    updateSlider()
    setActivePaginationDot()
}

const handleTouchMove = (e) => {
    if (!isSwiping.value) {
        return
    }

    const clientX = ref(e.touches ? e.touches[0].clientX : e.clientX)
    const delX = ref(clientX.value - startX.value)

    if (delX.value > 50) {
        prevSlide()
        isSwiping.value = false;
    } else if (delX.value < - 50) {
        nextSlide()
        isSwiping.value = false;
    }
}


const goToSlide = (index) => {
    currentIndex.value = index;
    updateSlider();
    setActivePaginationDot();
}

const handleTouchEnd = () => {
    isSwiping.value = false;
}

// destop product zoom 
const imgs = ref([])
const lens = ref([]);
const result = ref(null)
const cx = ref(null);
const cy = ref(null);
const windowObj = ref(null);

const imageZoomProduct = (index) => {
    cx.value = result.value.offsetWidth / lens.value[index].offsetWidth;
    cy.value = result.value.offsetHeight / lens.value[index].offsetHeight;

    result.value.style.backgroundImage = "url('" + imgs.value[index].src + "')";
    result.value.style.backgroundSize = (imgs.value[index].width * cx.value) + "px " + (imgs.value[index].height * cy.value) + "px";
}

const moveLens = (e, index) => {
    const pos = ref(null)
    const x = ref(null)
    const y = ref(null)

    e.preventDefault();

    pos.value = getCursorPos(e, index);

    x.value = pos.value.x - (lens.value[index].offsetWidth / 2);
    y.value = pos.value.y - (lens.value[index].offsetHeight / 2);

    if (x.value > imgs.value[index].width - lens.value[index].offsetWidth) {
        x.value = imgs.value[index].width - lens.value[index].offsetWidth;
    }

    if (x.value < 0) { x.value = 0; }

    if (y.value > imgs.value[index].height - lens.value[index].offsetHeight) {
        y.value = imgs.value[index].height - lens.value[index].offsetHeight;
    }

    if (y.value < 0) { y.value = 0; }

    lens.value[index].style.left = x.value + "px";
    lens.value[index].style.top = y.value + "px";

    result.value.style.backgroundPosition = "-" + (x.value * cx.value) + "px -" + (y.value * cy.value) + "px";
}

const getCursorPos = (e, index) => {
    const a = ref(null);
    const x = ref(0);
    const y = ref(0);

    a.value = imgs.value[index].getBoundingClientRect();

    x.value = e.pageX - a.value.left;
    y.value = e.pageY - a.value.top;

    x.value = x.value - windowObj.value.pageXOffset;
    y.value = y.value - windowObj.value.pageYOffset;

    return { x: x.value, y: y.value }
}

const isLensShow = ref(false);
</script>

<template>
    <!-- container -->
    <div class="overflow-hidden relative m-auto h-full" @touchstart="handleTouchStart($event)"
        @mousedown="handleTouchStart($event)" @touchmove="handleTouchMove($event)" @mousemove="handleTouchMove($event)"
        @touchend="handleTouchEnd($event)" @mouseup="handleTouchEnd($event)">

        <!-- wrapper -->
        <div class="border border-slate-100 bg-white rounded-lg">
            <div class="flex transition-transform duration-[0.3s] ease-[ease-in-out] relative" ref="wrapper">
                <div class="min-w-full p-10 slides cursor-pointer" v-for="(slide, index) in thumbnails" v-if="thumbnails"
                    :key="index" @click="$emit('openModal', index, slide.img ? 1 : 0)">
                    <div v-if="slide.img" class="w-fit mx-auto relative"
                        @mousemove="isLensShow = true, $emit('lensShow', true)"
                        @mouseleave="isLensShow = false, $emit('lensShow', false)">
                        <div class="absolute border border-neutral-300 w-28 h-28 border-solid xl:block hidden bg-[rgba(0,0,0,0.4)]"
                            ref="lens" @mousemove="moveLens($event, index)" @touchmove="moveLens($event, index)"
                            v-show="isLensShow == true"></div>
                        <img :src="slide.img" class="sm:max-h-[480px] max-h-[320px] h-full" :alt="slide" ref="imgs"
                            @mousemove="imageZoomProduct(index), moveLens($event, index)"
                            @touchmove="moveLens($event, index)">
                    </div>
                    <video class="h-full w-full p-2" controls v-else-if="slide.video">
                        <source :src="slide.video" type="video/mp4">
                    </video>
                </div>

            </div>
            <div class="flex justify-center items-center mb-2">
                <div v-for="(slideThumb, index ) in thumbnails" class="md:mx-2 mx-1 p-2 cursor-pointer rounded border-2"
                    :class="currentIndex == index ? 'border-[#6366f1]' : 'border-transparent'" ::key="index"
                    @click="goToSlide(index)">
                    <img :src="slideThumb.img" v-if="slideThumb.img" class="lg:max-h-[80px] max-h-[35px] h-full"
                        :alt="slideThumb" />
                    <div class="aspect-square" v-else-if="slideThumb.video">
                        <span>
                            <img src="../assets/images/play.png" class="xl:w-16 w-8 aspect-square" alt="Left-btn">
                        </span>
                    </div>
                </div>
            </div>
        </div>

        <!-- btn -->
        <button @click="prevSlide()"
            class="absolute -translate-y-[100%] text-lg cursor-pointer text-[#333] border-[none] top-2/4 rounded-full p-2 font-bold left-2.5">
            <img src="../assets/svgs/left-btn.svg" class="h-5 w-5" alt="Left-btn">
        </button>
        <button @click="nextSlide()"
            class="absolute -translate-y-[100%] text-lg cursor-pointer text-[#333] border-[none] top-2/4 rounded-full p-2 font-bold right-2.5">
            <img src="../assets/svgs/right-btn.svg" class="h-5 w-5" alt="Left-btn">
        </button>

        <!-- pagination -->
        <div class="text-center mt-5">
            <div class="inline-block w-2.5 h-2.5 cursor-pointer mx-[5px] my-0 rounded-[50%]"
                :class="currentIndex == index ? 'bg-red-500 border border-red-500' : 'bg-transparent border border-slate-500'"
                v-for="(slide, index) in slideArr" :key="index" @click="goToSlide(index)">
            </div>
        </div>


    </div>
</template>