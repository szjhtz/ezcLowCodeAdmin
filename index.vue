<template>
  <dark-mode-container class="global-tab flex-y-center w-full" :style="{ height: theme.tab.height + 'px' }">
    <!-- <span class="tabs-card-prev flex-center h-full cursor-pointer dark:hover:bg-[#333] hover:bg-[#f6f6f6]"
      :class="{ 'tabs-card-prev-hide': !scrollable }" @click="scrollPrev">
      <Icon icon="uil:angle-left-b" :size="theme.tab.height" class="inline-block align-text-bottom mr-4px text-16px" />
    </span> -->
    <div ref="bsWrapper" class="flex-1-hidden h-full">
      <better-scroll ref="bsScroll" :options="{ scrollX: true, scrollY: false, click: canClick }">
        <tab-detail @scroll="handleScroll" />
      </better-scroll>
    </div>
    <!-- <div ref="navScroll" class="flex-1-hidden h-full">
      <tab-detail />
    </div> -->
    <!-- <span class="tabs-card-next flex-center h-full cursor-pointer dark:hover:bg-[#333] hover:bg-[#f6f6f6]"
      :class="{ 'tabs-card-next-hide': !scrollable }" @click="scrollNext">
      <Icon icon="uil:angle-right-b" :size="theme.tab.height" class="inline-block align-text-bottom mr-4px text-16px" />
    </span> -->
    <reload-button />
  </dark-mode-container>
</template>

<script setup lang="ts">
// import { ref, watch, nextTick } from 'vue';
import { ref, watch } from 'vue';
import { useRoute } from 'vue-router';
import { useElementBounding } from '@vueuse/core';
import { useThemeStore, useTabStore } from '@/store';
import { useDeviceInfo } from '@/composables';
import { TabDetail, ReloadButton } from './components';
// import { Icon } from '@iconify/vue';

const route = useRoute();
const theme = useThemeStore();
const tab = useTabStore();
const deviceInfo = useDeviceInfo();

// const scrollable = ref(false);
// const navScroll: any = ref(null);

const bsWrapper = ref<HTMLElement>();
const { width: bsWrapperWidth, left: bsWrapperLeft } = useElementBounding(bsWrapper);

const bsScroll = ref<Expose.BetterScroll>();

const canClick = Boolean(deviceInfo.device.type);

function handleScroll(clientX: number) {
  const currentX = clientX - bsWrapperLeft.value;
  const deltaX = currentX - bsWrapperWidth.value / 2;
  if (bsScroll.value) {
    const { maxScrollX, x: leftX } = bsScroll.value.instance;
    const rightX = maxScrollX - leftX;
    const update = deltaX > 0 ? Math.max(-deltaX, rightX) : Math.min(-deltaX, -leftX);
    bsScroll.value?.instance.scrollBy(update, 0, 300);
  }
}
// /**
//  * @param value 要滚动到的位置
//  * @param amplitude 每次滚动的长度
//  */
// function scrollTo(value: number, amplitude: number) :any {
//   const currentScroll = navScroll.value.scrollLeft;
//   const scrollWidth = (amplitude > 0 && currentScroll + amplitude >= value) || (amplitude < 0 && currentScroll + amplitude <= value) ? value : currentScroll + amplitude;
//   navScroll.value && navScroll.value.scrollTo(scrollWidth, 0);
//   if (scrollWidth === value) return;
//   return window.requestAnimationFrame(() => scrollTo(value, amplitude));
// }

// function scrollPrev() {
//   console.info(navScroll);
//   const containerWidth = navScroll.value.offsetWidth;
//   const currentScroll = navScroll.value.scrollLeft;

//   if (!currentScroll) return;
//   const scrollLeft = currentScroll > containerWidth ? currentScroll - containerWidth : 0;
//   scrollTo(scrollLeft, (scrollLeft - currentScroll) / 20);
// }

// function scrollNext() {
//   const containerWidth = navScroll.value.offsetWidth;
//   const navWidth = navScroll.value.scrollWidth;
//   const currentScroll = navScroll.value.scrollLeft;

//   if (navWidth - currentScroll <= containerWidth) return;
//   const scrollLeft = navWidth - currentScroll > containerWidth * 2 ? currentScroll + containerWidth : navWidth - containerWidth;
//   scrollTo(scrollLeft, (scrollLeft - currentScroll) / 20);
// }
// /**
//  * @param autoScroll 是否开启自动滚动功能
//  */
// async function updateNavScroll(autoScroll?: boolean) {
//   await nextTick();
//   if (!navScroll.value) return;
//   const containerWidth = navScroll.value.offsetWidth;
//   const navWidth = navScroll.value.scrollWidth;

//   if (containerWidth < navWidth) {
//     scrollable.value = true;
//   }else{
//     scrollable.value = false;
//   }
// }
function init() {
  tab.iniTabStore(route);
  // updateNavScroll(true);
}

watch(
  () => route.path,
  () => {
    tab.addTab(route);
    tab.setActiveTab(route.path);
    // updateNavScroll();
  }
);

// 初始化
init();
</script>
<style scoped>
.global-tab {
  box-shadow: 0 1px 2px rgb(0 21 41 / 8%);
}
</style>
