<script setup lang="ts">
import { useDarkMode } from '@/composable/dark-mode'
import { useScreenSize } from '@/composable/screen-size'
import BaseNotification from '@/components/base-notification.vue'

useDarkMode()
useScreenSize()
</script>

<template>
  <router-view />
  <component :is="BaseNotification" />
  <modal-notification />
</template>
