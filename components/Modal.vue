<script setup>
const props = defineProps(
    {
        index: {
            default: 0
        },
        type: {
            default: 1
        },
        details: {
            required: true
        }
    }
)

const activeTab = ref(props.type)
const currentIndex = ref(props.index);

const firstVideoIndex = () => {
    activeTab.value = 0
    for (let i = 0; i < props.details.thumbnails.length; i++) {
        if (props.details.thumbnails[i].video) {
            currentIndex.value = i
            break;
        }
    }
}

const firstImageIndex = () => {
    activeTab.value = 1
    for (let i = 0; i < props.details.thumbnails.length; i++) {
        if (props.details.thumbnails[i].img) {
            currentIndex.value = i
            break;
        }
    }
}


const img = ref(null);
const imgMobile = ref(null);
const containerDiv = ref(null);
const containerMobile = ref(null);
const zoomFactor = ref(2)
const zoomFactorMobile = ref(2)
const isMouseClickOnImage = ref(false);
const isMouseClickOnImageMobile = ref(false);

// zoom in destop
const handleMouseMove = (e) => {
    const { left, top, width, height, right, bottom } = containerDiv.value.getBoundingClientRect();

    const x = ref(e.clientX - (right - (left + 300)))
    const y = ref(e.clientY - (bottom - (top + 200)))

    const xPercent = ref((x.value / width) * 100)
    const yPercent = ref((y.value / height) * 100)

    const offsetX = ref(((xPercent.value / 100) * (zoomFactor.value - 1)) * width)
    const offsetY = ref(((yPercent.value / 100) * (zoomFactor.value - 1)) * width)

    if (isMouseClickOnImage.value) {
        img.value.style.transform = `translate(${-offsetX.value}px, ${-offsetY.value}px) scale(${zoomFactor.value})`;
    }
}

// zoom in mobile
const zoomMobileImg = (e) => {
    console.log('zomm');
    const tranformVal = ref(imgMobile.value.style.transform);

    if (tranformVal.value) {
        const match = ref(tranformVal.value.match(/scale\((\d+(\.\d+)?)\)/))

        if (match.value && match.value[1]) {
            zoomFactorMobile.value = parseFloat(match.value[1]);
        }
    }

    imgMobile.value.style.transform = 'scale(' + zoomFactorMobile.value + ')';

    const boundingRect = ref(imgMobile.value.getBoundingClientRect());
    const x = ref(e.clientX - boundingRect.value.left);
    const y = ref(e.clientY - boundingRect.value.top);

    imgMobile.value.style.transformOrigin = (x.value / boundingRect.value.width) * 100 + '% ' + (y.value / boundingRect.value.height) * 100 + '%';
}

const resetZoom = () => {
    img.value.style.transform = 'none';
}

const resetMobileZoom = () => {
    imgMobile.value.style.transform = "none";
} 
</script>

<template>
    <div class="fixed z-[1] w-full h-full overflow-auto bg-[rgba(0,0,0,0.4)] xl:py-[50px] p-4 left-0 top-0"
        @click.self="$emit('closeModel') , isMouseClickOnImage = false , isMouseClickOnImageMobile = false">

        <!-- Modal content -->
        <div class="container mx-auto bg-white xl:overflow-hidden overflow-auto rounded min-h-full h-full relative py-3">
            <div class="h-fit w-fit top-0 right-2 cursor-pointer font-bold absolute xl:block hidden"
                @click="$emit('closeModel'), isMouseClickOnImage = false , isMouseClickOnImageMobile = false">
                &#x2715
            </div>

            <!-- destop model -->
            <div class="w-[97%] h-full mx-auto xl:flex flex-col hidden">
                <ul class="flex border-b border-gray-200 pb-1">
                    <li class="font-semibold uppercase text-sm mx-2 py-2 cursor-pointer"
                        :class="activeTab == 0 ? 'border-b border-red-400' : 'opacity-70'" @click="firstVideoIndex()">video
                    </li>
                    <li class="font-semibold uppercase text-sm mx-2 py-2 cursor-pointer"
                        :class="activeTab == 1 ? 'border-b border-red-400' : 'opacity-70'" @click="firstImageIndex()">images
                    </li>
                </ul>

                <div class="relative flex mt-1 max-h-full pt-4 pb-16">
                    <!-- img div -->
                    <div class="p-2 grow h-full overflow-hidden mx-auto" ref="containerDiv"
                        @click="isMouseClickOnImage = !isMouseClickOnImage, resetZoom()"
                        @mousemove="handleMouseMove($event)" @mouseleave="resetZoom()"
                        :class="activeTab == 1 ? isMouseClickOnImage ? 'cursor-zoom-out' : 'cursor-zoom-in' : ''">
                        <img :src="details.thumbnails[currentIndex].img" ref="img"
                            v-if="details.thumbnails[currentIndex].img && details.thumbnails[currentIndex] && activeTab == 1"
                            class="mx-auto h-full" alt="thumb.img">
                        <video class="h-full w-full p-2" controls
                            v-else-if="details.thumbnails[currentIndex].video && details.thumbnails[currentIndex] && activeTab == 0">
                            <source :src="details.thumbnails[currentIndex].video" type="video/mp4">
                        </video>
                    </div>

                    <!-- details div -->
                    <div class="w-96 pl-4">
                        <!-- product name -->
                        <h1 class="text-[24px] font-semibold leading-none capitalize">{{ details.name }}</h1>

                        <p class="md:text-sm opacity-70 text-base font-semibold py-2">
                            <span class="capitalize">Color Name : </span>
                            <span class="capitalize"> {{ details.details.color }} </span>
                        </p>

                        <div class="grid grid-cols-4 gap-4 mt-4">
                            <!-- for img tab -->
                            <template v-for="(thumb, ind) in details.thumbnails" :key="ind">

                                <div @click="currentIndex = ind" v-if="activeTab == 1 && thumb && thumb.img"
                                    class="border rounded p-2 cursor-pointer relative"
                                    :class="ind == currentIndex ? 'border-red-300' : ''">
                                    <img :src="thumb.img" v-if="thumb.img && thumb" class="aspect-square object-contain"
                                        style="transition:  transform 0.5;" alt="thumb.img">
                                </div>

                                <div class="aspect-square border rounded p-2 cursor-pointer"
                                    :class="ind == currentIndex ? 'border-red-300' : ''"
                                    v-else-if="thumb.video && thumb && activeTab == 0">
                                    <span>
                                        <img src="../assets/images/play.png" class="xl:w-16 w-8 aspect-square"
                                            alt="Left-btn">
                                    </span>
                                </div>
                            </template>
                        </div>
                    </div>
                </div>
            </div>

            <!-- mobile model -->
            <div class="h-full flex flex-col xl:hidden min-h-fit">
                <button class="shadow border py-2 px-4 w-fit font-bold rounded ml-2 "
                    @click="$emit('closeModel'),  isMouseClickOnImage = false , isMouseClickOnImageMobile = false">Back</button>

                <div class="grow pt-4" >

                    <div class="h-full max-h-[89%] mx-2 relative overflow-hidden p-4" ref="containerMobile"
                        @dblclick="isMouseClickOnImageMobile ? resetMobileZoom() : zoomMobileImg($event), isMouseClickOnImageMobile = !isMouseClickOnImageMobile">
                        <img :src="details.thumbnails[currentIndex].img" v-if="details.thumbnails[currentIndex].img"
                            class="h-full mx-auto object-contain aspect-square" ref="imgMobile" alt="details.thumbnails[currentIndex].img"
                            style="transition: transform 0.5s ease;">

                        <video class="h-full w-full py-2" controls
                            v-else-if="details.thumbnails[currentIndex].video && details.thumbnails[currentIndex]">
                            <source :src="details.thumbnails[currentIndex].video" type="video/mp4">
                        </video>
                    </div>
                    
                    <div class="overflow-y-auto w-full px-2 pt-2">
                        <div class="flex h-16 sm:h-20 flex-nowrap">
                            <div v-for="(thumb, ind) in details.thumbnails" :key="index"
                            class="aspect-square w-16 sm:w-20 p-1 grid place-items-center rounded"
                            :class="currentIndex == ind ? 'border border-red-500' : ''" @click="currentIndex = ind">
                            <img :src="thumb.img" v-if="thumb.img && thumb" class="aspect-square object-contain"
                                alt="thumb.img">
                            <span v-if="thumb && thumb.video">
                                <img src="../assets/images/play.png" class="xl:w-16 w-8 aspect-square" alt="Left-btn">
                            </span>
                        </div>
                    </div>
                </div>
            </div>

        </div>
    </div>

</div></template>