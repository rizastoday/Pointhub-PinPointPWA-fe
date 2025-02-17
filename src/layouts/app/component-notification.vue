<script setup lang="ts">
import BasePopper from '@/components/base-popper.vue'
import { onMounted, ref } from 'vue'
import { useUserStore } from '@/stores/auth'
import { useRouter } from 'vue-router'
import { openModalNotification } from '@/plugins/modal-notification'
import { UseInvitationStore } from '@/stores/invitation'

const activeTab = ref('all')
const user = useUserStore()
const router = useRouter()
const invitation = UseInvitationStore()

onMounted(() => {
  invitation.mockInvitation()
})
const acceptInvitation = () => {
  openModalNotification({
    show: true,
    title: 'Joined',
    content: `Success to join Group`,
    size: 'md',
    className: 'modal-invitation-accepted'
  })
}
const rejectInvitation = () => {
  openModalNotification({
    show: true,
    title: 'Rejected',
    content: `Success Reject invitation`,
    size: 'md',
    className: 'modal-invitation-rejected'
  })
}
</script>

<template>
  <component :is="BasePopper">
    <button class="relative rounded-full p-2 hover:bg-slate-300/20 dark:hover:bg-slate-300/20">
      <i class="i-ph-bell-ringing h-24px w-24px flex items-center justify-center" />
      <!-- ping -->
      <span
        class="absolute right-px top-px h-2 w-2 flex items-center justify-center"
        v-if="user.role == 'user'"
      >
        <span
          class="absolute h-full w-full inline-flex animate-ping rounded-full bg-red-400 opacity-80"
        ></span>
        <span class="h-2 w-2 inline-flex rounded-full bg-red-400"></span>
      </span>
    </button>
    <template #content>
      <div
        class="popper-box shadow-soft dark:shadow-soft-dark mx-4 mt-1 max-h-[calc(100vh-6rem)] w-[calc(100vw-2rem)] flex flex-col border border-slate-200 rounded-lg bg-white sm:m-0 sm:w-80 dark:border-slate-800 dark:bg-slate-700"
      >
        <div class="rounded-t-lg bg-slate-100 text-slate-600 dark:bg-slate-800 dark:text-slate-200">
          <div class="flex items-center justify-between px-4 pt-2">
            <div class="flex items-center p-3">
              <h3 class="font-medium text-slate-700 dark:text-slate-100">Notifications</h3>
              <div
                v-if="user.role == 'user'"
                class="badge h-5 rounded-full bg-primary/10 px-1.5 text-primary dark:bg-slate/15 dark:text-slate"
              >
                {{ invitation.invitations.length }}
              </div>
            </div>
          </div>
        </div>
        <div class="flex flex-col overflow-hidden">
          <div v-if="user.role == 'user'" class="overflow-y-auto p-4 space-y-4">
            <div class="bg-slate-300/20 rounded-5 p-5 mt-5" v-for="item in invitation.invitations">
              <div class="flex flex-col gap-2">
                <small class="capitalize">{{ item.from }} has invite you to join group</small>
                <div class="flex flex-col gap-5">
                  <div class="p-3 border-secondary border-1 rounded-5 flex-1">
                    <table class="w-full">
                      <tr>
                        <td>Group Name</td>
                        <td>:</td>
                        <td>{{ item.inviteGroup }}</td>
                      </tr>
                      <tr>
                        <td>Role</td>
                        <td>:</td>
                        <td>{{ item.inviteRole }}</td>
                      </tr>
                    </table>
                  </div>
                  <div class="flex flex-row justify-center items-center flex-1 gap-2">
                    <button
                      class="bg-blue text-center py-2 px-10 text-white capitalize rounded-5 capitalize accept"
                      @click.prevent="acceptInvitation"
                    >
                      join
                    </button>
                    <button
                      class="bg-transparent border-1 border-slate-800 dark:border-slate-500 text-center py-2 px-10 text-slate-800 dark:text-white capitalize rounded-5 capitalize reject"
                      @click.prevent="rejectInvitation"
                    >
                      reject
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div v-else class="overflow-auto p-4">
            <p>No Notification</p>
          </div>
        </div>
      </div>
    </template>
  </component>
</template>
