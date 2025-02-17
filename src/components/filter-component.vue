<script setup lang="ts">
import baseDatePicker from '@/components/base-datepicker.vue'
import baseInput from '@/components/base-input.vue'
import BaseModal from '@/components/base-modal.vue'
import baseButton from '@/components/base-button.vue'
import { useUserStore } from '@/stores/auth'
import { computed, reactive } from 'vue'
import { openModalNotification } from '@/plugins/modal-notification'

const user = useUserStore()
const props = withDefaults(
  defineProps<{
    filterDateFrom: string
    filterDateTo: string
    filterUser: string[]
  }>(),
  {}
)
const emits = defineEmits<{
  (e: 'update:filterDateFrom', value: string): void
  (e: 'update:filterDateTo', value: string): void
  (e: 'update:filterUser', value: string[]): void
  (e: 'applyFilter'): void
}>()
const valueFromDate = computed({
  get() {
    return props.filterDateFrom
  },
  set(value: string) {
    emits('update:filterDateFrom', value)
  }
})
const valueFromTo = computed({
  get() {
    return props.filterDateTo
  },
  set(value: string) {
    emits('update:filterDateTo', value)
  }
})
const valueUser = computed({
  get() {
    return props.filterUser
  },
  set(value: string[]) {
    emits('update:filterUser', value)
  }
})

const filterModel = reactive({
  show: false,
  searchUser: ''
})
const filteredUser = computed(() => {
  let data = user.users.filter((u) =>
    u.toLowerCase().includes(filterModel.searchUser.toLowerCase())
  )
  if (user.role == 'user') {
    data = data.filter((x) => x.toLowerCase() == user.username.toLowerCase())
  }
  return data
})
const isFiltered = computed(
  () => valueUser.value.length > 0 || valueFromDate.value || valueFromTo.value
)
const filterAllUser = () => {
  valueUser.value.length > 0 ? valueUser.value.splice(0) : valueUser.value.push(...user.users)
}

const applyFilter = () => {
  emits('applyFilter')
  openModalNotification({
    show: true,
    title: 'Applied',
    content: `Filter Applied`,
    size: 'md',
    className: 'modal-filter-date-attendance-success'
  })
  filterModel.show = false
}
</script>

<template>
  <baseButton
    class-name=" w-full bg-white dark:bg-slate-800 !text-slate-5 !dark:text-slate border-1 border-slate flex flex-row items-center gap-3 whitespace-nowrap"
    type="submit"
    @click.prevent="filterModel.show = true"
  >
    <span class="hidden lg:block">Filter</span>
    <i class="block" :class="isFiltered ? 'i-fas-filter text-danger' : 'i-far-filter'"></i>
  </baseButton>

  <Teleport to="body">
    <!-- filter date -->
    <component
      :is="BaseModal"
      :is-open="filterModel.show"
      @on-close="filterModel.show = false"
      size="lg"
    >
      <template #content>
        <div class="max-h-90vh overflow-auto p-4 modal-filter-date-attendance">
          <h2 class="py-4 text-2xl font-bold">Filter</h2>
          <form class="gap-5 filter-date-attendance" @submit.prevent="applyFilter">
            <div class="flex flex-row gap-2">
              <component
                :is="baseDatePicker"
                v-model="valueFromDate"
                label="From"
                class="w-full"
                name="startDate"
              />
              <component
                :is="baseDatePicker"
                v-model="valueFromTo"
                label="To"
                class="w-full"
                name="endDate"
              />
            </div>
            <component
              :is="baseInput"
              v-model="filterModel.searchUser"
              label="Search User"
              class="w-full"
              mode="bordered"
            />

            <button class="btn mt-3 btn-secondary mx-a !px-2 !py-1" @click.prevent="filterAllUser">
              All User
            </button>
            <div class="flex flex-col max-h-[20vh] h-20vh py-3 overflow-auto">
              <label v-for="item in filteredUser">
                <input
                  type="checkbox"
                  class="border-blue mr-1"
                  v-model="valueUser"
                  :value="item"
                  id="filter-person"
                />
                {{ item }}
              </label>
            </div>
            <div class="flex flex-col">
              <baseButton class="bg-blue w-full" type="submit">Save</baseButton>
              <baseButton
                class="bg-secondary w-full"
                type="button"
                @click="filterModel.show = false"
              >
                Cancel
              </baseButton>
            </div>
          </form>
        </div>
      </template>
    </component>
  </Teleport>
</template>
