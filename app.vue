<template>
  <div>
    <NuxtLayout>
      <NuxtPage/>
    </NuxtLayout>
  </div>
</template>

<script setup>
onMounted(() => {
  const body = document.getElementsByTagName('body');
  body[0].setAttribute('class', 'bg-[#f5f5f5]');
})
</script>
