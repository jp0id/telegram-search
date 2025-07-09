<script setup lang="ts">
import { inject, computed, type ComputedRef } from 'vue'
import { cn } from '../../lib/utils'

// Sidebar context
interface SidebarState {
  open: ComputedRef<boolean>
  setOpen: (value: boolean) => void
  isMobile: ComputedRef<boolean>
  openMobile: ComputedRef<boolean>
  setOpenMobile: (value: boolean) => void
}

interface SidebarInsetProps {
  class?: string
}

const props = defineProps<SidebarInsetProps>()

// Inject sidebar context
const sidebarContext = inject<SidebarState>('sidebar')
if (!sidebarContext) {
  throw new Error('SidebarInset must be used within SidebarProvider')
}

const { isMobile, openMobile, setOpenMobile } = sidebarContext

// Close mobile sidebar when clicking on backdrop
const handleBackdropClick = (): void => {
  if (isMobile.value && openMobile.value) {
    setOpenMobile(false)
  }
}
</script>

<template>
  <main :class="cn('relative flex min-h-svh flex-1 flex-col bg-background peer-data-[variant=inset]:min-h-[calc(100svh-theme(spacing.4))] md:peer-data-[variant=inset]:m-2 md:peer-data-[state=collapsed]:peer-data-[variant=inset]:ml-2 md:peer-data-[variant=inset]:ml-0 md:peer-data-[variant=inset]:rounded-xl md:peer-data-[variant=inset]:shadow', props.class)">
    <!-- Mobile backdrop -->
    <div
      v-if="isMobile && openMobile"
      class="fixed inset-0 z-30 bg-black/50 transition-opacity"
      @click="handleBackdropClick"
    />
    
    <slot />
  </main>
</template>