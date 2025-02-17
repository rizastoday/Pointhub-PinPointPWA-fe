<script setup lang="ts">
import { useSidebar } from '@/composable/sidebar'
import { useSidebarStore } from '@/stores/sidebar'
import { useSidebarMenuStore } from '@/stores/sidebar-menu'
import { useMobileBreakpoint } from '@/composable/mobile-breakpoint'
import { useRoute } from 'vue-router'
import { watch } from 'vue'

const sidebarMenuStore = useSidebarMenuStore()
const sidebarStore = useSidebarStore()
const route = useRoute()
const { isMobileBreakpoint } = useMobileBreakpoint()
useSidebar()

const isActiveRoutes = (name: string) => {
  return name.toLowerCase() == route.name?.toString().toLowerCase()
}

const isActiveParent = (name: string) => {
  return name.toLowerCase() == route.meta.parentName?.toString().toLowerCase()
}

watch(
  route,
  (r) => {
    r.meta.parentName
      ? sidebarMenuStore.toggleMenu(r.meta.parentName.toString() as string)
      : sidebarMenuStore.toggleMenu(r.name?.toString() as string)
  },
  {
    deep: true,
    immediate: true
  }
)
</script>

<template>
  <div class="sidebar-panel">
    <div class="sidebar-panel-container">
      <!-- Sidebar Panel Header -->
      <div class="sidebar-panel-header">
        <p class="text-base font-extrabold tracking-wider text-slate-100">
          {{ sidebarMenuStore.shortcut[sidebarMenuStore.activeShortcutIndex]?.displayName }}
        </p>
        <button @click="sidebarStore.closeSidebar()" v-if="isMobileBreakpoint()" class="mr-2">
          <div v-if="sidebarStore.isSidebarOpen">
            <i class="i-fas-angle-left block text-white"></i>
          </div>
        </button>
      </div>
      <!-- Sidebar Panel Body -->

      <!-- Sidebar Panel Body -->
      <div class="sidebar-panel-body">
        <ul class="flex flex-1 flex-col px-4">
          <li
            v-for="menu in sidebarMenuStore.shortcut[sidebarMenuStore.activeShortcutIndex].menu"
            :key="menu.name"
          >
            <!-- Sub Menu Wrapper -->
            <template v-if="menu.submenu">
              <router-link
                v-if="menu.path"
                :to="menu.path"
                class="menu-link-button"
                :class="isActiveRoutes(menu.name) ? 'font-extrabold' : ''"
                @click="sidebarMenuStore.toggleMenu(menu.name)"
              >
                <p>{{ menu.name }}</p>
                <i
                  v-if="menu.submenu"
                  class="i-fas-angle-right block"
                  :class="{
                    'rotate-90 transition transform-gpu':
                      sidebarMenuStore.$state.activeMenuName === menu.name
                  }"
                />
              </router-link>
              <div
                v-else
                class="menu-link-button"
                @click="sidebarMenuStore.toggleMenu(menu.name)"
                :class="isActiveParent(menu.name) ? 'font-extrabold' : ''"
              >
                <p>{{ menu.name }}</p>
                <i
                  v-if="menu.submenu"
                  class="i-fas-angle-right block"
                  :class="{
                    'rotate-90 transition transform-gpu':
                      sidebarMenuStore.$state.activeMenuName === menu.name
                  }"
                />
              </div>
            </template>
            <template v-else>
              <!-- Internal Menu -->
              <router-link
                v-if="menu.path"
                :to="menu.path as string"
                class="menu-link-button"
                :class="isActiveRoutes(menu.name) ? 'font-extrabold' : ''"
              >
                {{ menu.name }}
              </router-link>
              <!-- External Menu -->
              <a
                v-if="menu.link"
                :href="menu.link as string"
                target="_blank"
                class="menu-link-button !text-slate-200/80"
              >
                {{ menu.name }}
                <i class="i-fas-up-right-from-square block"></i>
              </a>
            </template>
            <div v-if="menu.submenu && menu.submenu.length > 0">
              <ul
                class="submenu"
                :class="{
                  'max-h-[1000px]! overflow-auto bg-slate-700 p-1 rounded-lg':
                    menu.name === sidebarMenuStore.$state.activeMenuName
                }"
              >
                <li v-for="submenu in menu.submenu" :key="submenu.name" class="overflow-hidden">
                  <router-link :to="submenu.path as string" class="submenu-link">
                    <div class="flex items-center space-x-2">
                      <div class="bullet-list"></div>
                      <p>{{ submenu.name }}</p>
                    </div>
                  </router-link>
                  <div v-if="submenu.separator" class="submenu-separator"></div>
                </li>
              </ul>
            </div>
            <div v-if="menu.separator" class="menu-separator"></div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<style scoped>
.sidebar-panel {
  @apply fixed top-0 left-[var(--sidebar-shortcut-width)] h-full w-[var(--sidebar-panel-width)] z-30 transform-gpu transition-transform duration-200 opacity-100;
}

.is-sidebar-open .sidebar-panel {
  @apply -translate-x-[15rem] ease-out;
}

.sidebar-panel-container {
  @apply flex h-full grow flex-col bg-slate-800;
}

.sidebar-panel-header {
  @apply flex h-16 w-full items-center justify-between pl-4 pr-1;
}

.sidebar-panel-body {
  @apply h-[calc(100%-4.5rem)] overflow-x-hidden pb-6 mt-4;
}

.sidebar-panel-body .menu-separator {
  @apply my-3 h-px bg-slate-700;
}

.sidebar-panel-body .submenu-separator {
  @apply my-1 h-px bg-slate-600;
}

.sidebar-panel-body .bullet-list {
  @apply h-1.5 w-1.5 rounded-full border border-current opacity-40;
}

.sidebar-panel-body .menu-link-button {
  @apply flex w-full items-center justify-between py-2 outline-none duration-300 ease-in-out text-slate-200 hover:text-slate-50 cursor-pointer hover:bg-slate-700 focus:bg-slate-700 active:bg-slate-700/25 px-2 rounded-md;
}

.sidebar-panel-body .submenu-link {
  @apply flex items-center justify-between p-2 text-md  outline-none duration-300 ease-in-out hover:pl-4 text-slate-200 hover:text-slate-50;
}

.menu-link-button.router-link-active > i {
  @apply rotate-90 transition transform-gpu;
}

.submenu-link.router-link-active > .bullet-list {
  @apply bg-white;
}

.submenu-link.router-link-active p {
  @apply font-extrabold;
}

.submenu {
  @apply transform-gpu transition-all duration-300 max-h-0 overflow-hidden;
}

.submenu.router-link-active {
  @apply max-h-[1000px] overflow-auto bg-slate-700 p-1 rounded-lg;
}
</style>
