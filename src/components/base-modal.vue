<script setup lang="ts">
import { onMounted, ref, watch } from 'vue'
import { TransitionRoot, TransitionChild, Dialog, DialogPanel } from '@headlessui/vue'

export type SizeType = 'sm' | 'md' | 'lg' | 'xl' | '2xl' | 'full' | 'maximize'

const props = withDefaults(
  defineProps<{
    isOpen: boolean
    size?: SizeType
  }>(),
  {
    size: 'md'
  }
)

const isOpen = ref(props.isOpen)

const emit = defineEmits(['onClose'])

const close = () => {
  isOpen.value = false
}

watch(isOpen, (newData) => {
  if (newData === false) {
    emit('onClose')
  }
})

watch(props, () => {
  isOpen.value = props.isOpen
})

const clearButton = ref()
</script>

<template>
  <TransitionRoot appear :show="isOpen" as="template">
    <Dialog as="div" class="relative z-60" :initial-focus="clearButton">
      <!-- The backdrop, rendered as a fixed sibling to the panel container -->
      <div class="fixed inset-0 bg-black/40" aria-hidden="true" />

      <TransitionChild
        as="template"
        enter="duration-300 ease-out"
        enter-from="opacity-0"
        enter-to="opacity-100"
        leave="duration-200 ease-in"
        leave-from="opacity-100"
        leave-to="opacity-0"
      >
        <div class="fixed inset-0 bg-black bg-opacity-25" />
      </TransitionChild>

      <div class="fixed inset-0 overflow-y-auto">
        <div class="min-h-full flex items-center justify-center text-center">
          <TransitionChild
            as="template"
            enter="duration-300 ease-out"
            enter-from="opacity-0 scale-95"
            enter-to="opacity-100 scale-100"
            leave="duration-200 ease-in"
            leave-from="opacity-100 scale-100"
            leave-to="opacity-0 scale-95"
          >
            <DialogPanel
              class="w-full transform overflow-hidden rounded-2xl bg-white dark:bg-slate-800 text-left align-middle shadow-xl transition-all"
              :class="{
                'max-w-sm': props.size === 'sm',
                'max-w-md': props.size === 'md',
                'max-w-lg': props.size === 'lg',
                'max-w-xl': props.size === 'xl',
                'max-w-2xl': props.size === '2xl',
                'max-w-90%': props.size === 'full',
                'w-100vh h-100vh rounded-none': props.size === 'maximize'
              }"
            >
              <button
                type="button"
                @click="close()"
                class="btn absolute top-2 right-0"
                ref="clearButton"
              >
                <i class="i-fas-xmark block"></i>
              </button>
              <slot name="content"></slot>
            </DialogPanel>
          </TransitionChild>
        </div>
      </div>
    </Dialog>
  </TransitionRoot>
</template>
