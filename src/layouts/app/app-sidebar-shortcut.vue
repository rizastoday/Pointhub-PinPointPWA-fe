<script setup lang="ts">
import { useSidebarStore } from '@/stores/sidebar'
import { useSidebarMenuStore, type ShortcutInterface } from '@/stores/sidebar-menu'
import { onMounted, ref, watch } from 'vue'
import { useRoute } from 'vue-router'

const sidebarMenuStore = useSidebarMenuStore()
const sidebarStore = useSidebarStore()
const route = useRoute()

const setActiveRoute = (displayName: string) => {
  let active =
    (route?.meta.displayName?.toString().toLowerCase() as string) == displayName.toLowerCase()
  if (active) {
    sidebarMenuStore.activeShortcut = sidebarMenuStore.shortcut.find(
      (x) => x.displayName == displayName
    ) as ShortcutInterface
    sidebarMenuStore.activeShortcutIndex = sidebarMenuStore.shortcut.findIndex(
      (x) => x.displayName == displayName
    )
  }

  return active
}

onMounted(() => {
  setActiveRoute(route.meta.displayName as string)
})

const htmlTag = document.getElementsByTagName('html')[0]
const isDarkMode = ref(htmlTag.classList.contains('dark'))

function toggleDarkMode() {
  if (htmlTag.classList.contains('dark')) {
    htmlTag.classList.remove('dark')
    localStorage.setItem('dark-mode', 'light')
  } else {
    htmlTag.classList.add('dark')
    localStorage.setItem('dark-mode', 'dark')
  }

  isDarkMode.value = htmlTag.classList.contains('dark')
}
</script>

<template>
  <div class="sidebar-shortcut" :class="{ 'delay-100 duration-200': !sidebarStore.isSidebarOpen }">
    <div class="sidebar-shortcut-container">
      <div class="flex pt-4">
        <router-link to="/">
          <img
            class="sidebar-logo"
            src="https://assets.pointhub.net/assets/images/logo/primary/icon-circle.png"
            alt="logo"
          />
        </router-link>
      </div>
      <div class="sidebar-shortcut-body">
        <router-link
          v-for="shortcut in sidebarMenuStore.shortcut"
          :key="shortcut.icon"
          :to="shortcut.path as string"
          class="sidebar-shortcut-link"
          :class="{
            active: setActiveRoute(shortcut.displayName as string)
          }"
        >
          <i :class="`block text-2xl ${shortcut.icon}`"></i>
        </router-link>
      </div>
      <div class="my-2 flex flex-col gap-2">
        <button class="sidebar-shortcut-link" @click="toggleDarkMode()">
          <i class="i-far-moon block text-2xl"></i>
        </button>
        <button class="sidebar-shortcut-link">
          <i class="i-far-power-off block text-2xl"></i>
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.sidebar-logo {
  @apply h-10 w-10 transition-transform duration-500 hover:rotate-[360deg];
}

.sidebar-shortcut {
  @apply fixed w-[var(--sidebar-shortcut-width)] h-full z-40  opacity-100 bg-slate-700;
}

.sidebar-shortcut-container {
  @apply flex h-full w-full flex-col items-center border-r bg-slate-800 border-slate-700;
}

.sidebar-shortcut-body {
  @apply flex grow flex-col space-y-4 overflow-y-auto pt-6 text-slate-200;
}

.is-sidebar-open .sidebar-shortcut {
  @apply translate-x-0 ease-out;
}

.sidebar-shortcut-link {
  @apply flex h-11 w-11 items-center justify-center rounded-lg outline-none transition-colors duration-200 hover:bg-slate-300/20 focus:bg-slate-300/20 active:bg-slate-300/25;
}

/* .sidebar-shortcut-link.router-link-active {
  @apply bg-slate-300/20;
} */
.sidebar-shortcut-link.active {
  @apply font-bold text-slate-2 bg-slate-300/20 dark:hover:text-slate-900;
}
</style>
