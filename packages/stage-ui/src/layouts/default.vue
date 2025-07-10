<script lang="ts" setup>
import type { ChatGroup } from '@tg-search/client'

import { useAuthStore, useChatStore, useSettingsStore, useWebsocketStore } from '@tg-search/client'
import { useDark } from '@vueuse/core'
import { storeToRefs } from 'pinia'
import { computed, ref, watch } from 'vue'
import { RouterView, useRoute, useRouter } from 'vue-router'

import ChatsCollapse from '../components/layout/ChatsCollapse.vue'
import SettingsDialog from '../components/layout/SettingsDialog.vue'
import SidebarSelector from '../components/layout/SidebarSelector.vue'
import Avatar from '../components/ui/Avatar.vue'
import { Button } from '../components/ui/Button'
import {
  Sidebar,
  SidebarContent,
  SidebarFooter,
  SidebarHeader,
  SidebarInset,
  SidebarMenu,
  SidebarProvider,
  SidebarTrigger,
} from '../components/ui/Sidebar'

const settingsStore = useSettingsStore()
const { theme } = storeToRefs(settingsStore)
const isDark = useDark()

const websocketStore = useWebsocketStore()
const authStore = useAuthStore()
const { isLoggedIn } = storeToRefs(authStore)

const router = useRouter()
const route = useRoute()

const settingsDialog = ref(false)
const searchParams = ref('')

const chatStore = useChatStore()
const chats = computed(() => chatStore.chats)
const chatsFiltered = computed(() => {
  return chats.value.filter(chat => chat.name.toLowerCase().includes(searchParams.value.toLowerCase()))
})

const { selectedGroup } = storeToRefs(useSettingsStore())
const activeChatGroup = computed(() => {
  if (route.params.chatId) {
    const currentChat = chatStore.getChat(route.params.chatId.toString())
    if (currentChat) {
      return currentChat.type
    }
  }
  return selectedGroup.value
})

watch(theme, (newTheme) => {
  document.documentElement.setAttribute('data-theme', newTheme)
}, { immediate: true })

function toggleSettingsDialog(): void {
  settingsDialog.value = !settingsDialog.value
}

function toggleActiveChatGroup(group: ChatGroup): void {
  selectedGroup.value = group
}
</script>

<template>
  <SidebarProvider>
    <div class="min-h-screen w-full flex">
      <!-- Sidebar -->
      <Sidebar class="border-r border-border">
        <!-- Sidebar Header with Search -->
        <SidebarHeader class="p-4">
          <div class="relative">
            <div class="i-lucide-search absolute left-3 top-1/2 h-4 w-4 text-muted-foreground -translate-y-1/2" />
            <input
              v-model="searchParams"
              type="text"
              class="w-full border border-input rounded-md bg-background px-3 py-2 pl-9 text-sm ring-offset-background transition-colors disabled:cursor-not-allowed file:border-0 file:bg-transparent file:text-sm placeholder:text-muted-foreground file:font-medium disabled:opacity-50 focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-2 focus-visible:ring-ring"
              placeholder="Search"
            >
          </div>
        </SidebarHeader>

        <!-- Sidebar Content -->
        <SidebarContent>
          <!-- Navigation Menu -->
          <SidebarMenu class="mb-4">
            <SidebarSelector
              path="/"
              icon="i-lucide-home"
              name="主页"
            />
            <SidebarSelector
              path="/sync"
              icon="i-lucide-refresh-cw"
              name="同步"
            />
            <SidebarSelector
              path="/search"
              icon="i-lucide-search"
              name="搜索"
            />
            <SidebarSelector
              path="/settings"
              icon="i-lucide-settings"
              name="设置"
            />
          </SidebarMenu>

          <!-- Chat Groups -->
          <div class="h-full flex flex-1 flex-col justify-start overflow-y-auto border-t border-border pt-4">
            <ChatsCollapse
              class="max-h-[85%] flex flex-col"
              :class="{ 'flex-1': activeChatGroup === 'user' }"
              name="用户"
              icon="i-lucide-user"
              type="user"
              :chats="chatsFiltered.filter(chat => chat.type === 'user')"
              :active="activeChatGroup === 'user'"
              @update:toggle-active="toggleActiveChatGroup('user')"
            />

            <ChatsCollapse
              class="max-h-[85%] flex flex-col"
              :class="{ 'flex-1': activeChatGroup === 'group' }"
              name="群组"
              icon="i-lucide-users"
              type="group"
              :chats="chatsFiltered.filter(chat => chat.type === 'group')"
              :active="activeChatGroup === 'group'"
              @update:toggle-active="toggleActiveChatGroup('group')"
            />

            <ChatsCollapse
              class="max-h-[85%] flex flex-col"
              :class="{ 'flex-1': activeChatGroup === 'channel' }"
              name="频道"
              icon="i-lucide-message-circle"
              type="channel"
              :chats="chatsFiltered.filter(chat => chat.type === 'channel')"
              :active="activeChatGroup === 'channel'"
              @update:toggle-active="toggleActiveChatGroup('channel')"
            />
          </div>
        </SidebarContent>

        <!-- Sidebar Footer with User Profile -->
        <SidebarFooter class="border-t border-border p-4">
          <div class="flex items-center justify-between">
            <div class="mr-3 flex items-center gap-3">
              <div class="h-8 w-8 flex items-center justify-center overflow-hidden rounded-full bg-muted">
                <Avatar
                  :name="websocketStore.getActiveSession()?.me?.username"
                  size="sm"
                />
              </div>
              <div class="flex flex-col">
                <span class="whitespace-nowrap text-sm font-medium">{{ websocketStore.getActiveSession()?.me?.username }}</span>
                <span class="whitespace-nowrap text-xs text-muted-foreground">{{ websocketStore.getActiveSession()?.isConnected ? '已链接' : '未链接' }}</span>
              </div>
            </div>

            <!-- Control buttons -->
            <div class="flex items-center gap-2">
              <Button
                :icon="isDark ? 'i-lucide-sun' : 'i-lucide-moon'"
                variant="ghost"
                size="sm"
                class="h-8 w-8 p-0"
                :title="isDark ? '切换到亮色模式' : '切换到暗色模式'"
                @click="() => { isDark = !isDark }"
              />

              <Button
                icon="i-lucide-settings"
                variant="ghost"
                size="sm"
                class="h-8 w-8 p-0"
                title="设置"
                @click="toggleSettingsDialog"
              />
            </div>
          </div>
        </SidebarFooter>
      </Sidebar>

      <!-- Main Content Area -->
      <SidebarInset class="flex-1">
        <!-- Mobile trigger and login banner -->
        <header class="h-16 flex shrink-0 items-center gap-2 border-b border-border px-4">
          <SidebarTrigger class="-ml-1" />

          <!-- Login prompt banner -->
          <div
            v-if="!isLoggedIn"
            class="flex flex-1 items-center justify-center gap-2 rounded-md bg-yellow-500 px-3 py-1 text-sm text-yellow-900 font-medium"
          >
            <div class="i-lucide-alert-triangle h-4 w-4" />
            <span>请先登录 Telegram 账号以使用完整功能</span>
            <Button
              size="sm"
              icon="i-lucide-user"
              class="ml-2 h-7 border-yellow-700 bg-yellow-600 text-yellow-100 hover:bg-yellow-700"
              @click="router.push('/login')"
            >
              去登录
            </Button>
          </div>
        </header>

        <!-- Page Content -->
        <div class="flex flex-1 flex-col overflow-auto p-4">
          <RouterView :key="$route.fullPath" />
        </div>
      </SidebarInset>
    </div>

    <SettingsDialog
      v-model:show-dialog="settingsDialog"
    />
  </SidebarProvider>
</template>
