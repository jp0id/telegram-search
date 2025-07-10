<script setup lang="ts">
import { computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'

import { SidebarMenuButton, SidebarMenuItem } from '../ui/Sidebar'

interface SidebarSelectorProps {
  path: string
  icon: string
  name: string
}

const props = defineProps<SidebarSelectorProps>()

const router = useRouter()
const route = useRoute()

const isCurrentPage = computed(() => route.path === props.path)

function handleClick(): void {
  router.push(props.path)
}
</script>

<template>
  <SidebarMenuItem>
    <SidebarMenuButton
      :is-active="isCurrentPage"
      as-child
      @click="handleClick"
    >
      <div class="w-full flex cursor-pointer items-center gap-3">
        <span :class="icon" class="h-4 w-4 flex-shrink-0" />
        <span>{{ name }}</span>
      </div>
    </SidebarMenuButton>
  </SidebarMenuItem>
</template>
