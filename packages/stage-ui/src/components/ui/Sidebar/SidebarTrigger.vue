<script setup lang="ts">
import type { ComputedRef } from 'vue'

import { inject } from 'vue'

import { cn } from '../../../lib/utils'
import { Button } from '../Button'

// Sidebar context
interface SidebarState {
  open: ComputedRef<boolean>
  setOpen: (value: boolean) => void
  isMobile: ComputedRef<boolean>
  openMobile: ComputedRef<boolean>
  setOpenMobile: (value: boolean) => void
}

interface SidebarTriggerProps {
  class?: string
}

const props = defineProps<SidebarTriggerProps>()

// Inject sidebar context
const sidebarContext = inject<SidebarState>('sidebar')
if (!sidebarContext) {
  throw new Error('SidebarTrigger must be used within SidebarProvider')
}

const { isMobile, open, setOpen, openMobile, setOpenMobile } = sidebarContext

function handleClick(): void {
  if (isMobile.value) {
    setOpenMobile(!openMobile.value)
  }
  else {
    setOpen(!open.value)
  }
}
</script>

<template>
  <Button
    :icon="isMobile ? 'i-lucide-menu' : 'i-lucide-panel-left'"
    :class="cn(
      'h-7 w-7 flex items-center justify-center',
      props.class,
    )"
    variant="ghost"
    size="sm"
    @click="handleClick"
  >
    <span class="sr-only">Toggle Sidebar</span>
  </Button>
</template>
