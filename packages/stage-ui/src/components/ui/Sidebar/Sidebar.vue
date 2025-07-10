<script setup lang="ts">
import type { ComputedRef } from 'vue'

import { computed, inject } from 'vue'

import { cn } from '../../../lib/utils'

// Sidebar context
interface SidebarState {
  open: ComputedRef<boolean>
  setOpen: (value: boolean) => void
  isMobile: ComputedRef<boolean>
  openMobile: ComputedRef<boolean>
  setOpenMobile: (value: boolean) => void
}

// Props
interface SidebarProps {
  side?: 'left' | 'right'
  variant?: 'sidebar' | 'floating' | 'inset'
  collapsible?: 'offcanvas' | 'icon' | 'none'
  class?: string
}

const props = withDefaults(defineProps<SidebarProps>(), {
  side: 'left',
  variant: 'sidebar',
  collapsible: 'offcanvas',
})

// Inject sidebar context
const sidebarContext = inject<SidebarState>('sidebar')
if (!sidebarContext) {
  throw new Error('Sidebar must be used within SidebarProvider')
}

const { open, isMobile, openMobile } = sidebarContext

// Computed classes
const sidebarClasses = computed(() => {
  const baseClasses = 'flex h-full w-[--sidebar-width] flex-col'

  if (isMobile.value) {
    return cn(
      baseClasses,
      'fixed inset-y-0 z-40 w-[--sidebar-width-mobile] transform transition-transform duration-300 ease-in-out',
      props.side === 'left' ? 'left-0' : 'right-0',
      openMobile.value
        ? 'translate-x-0'
        : props.side === 'left'
          ? '-translate-x-full'
          : 'translate-x-full',
      'bg-background border-r border-border dark:bg-gray-800 dark:border-gray-700',
      props.class,
    )
  }

  return cn(
    baseClasses,
    'bg-background border-r border-border dark:bg-gray-800 dark:border-gray-700',
    props.variant === 'floating' && 'rounded-lg shadow-md',
    props.variant === 'inset' && 'rounded-lg border-2',
    props.class,
  )
})
</script>

<template>
  <aside
    :class="sidebarClasses"
    :data-sidebar="props.variant"
    :data-collapsible="props.collapsible"
    :data-state="isMobile ? (openMobile ? 'open' : 'closed') : (open ? 'open' : 'closed')"
    :data-side="props.side"
  >
    <slot />
  </aside>
</template>

<style scoped>
/* Custom CSS variables for sidebar theming */
:global(:root) {
  --sidebar-width: 16rem;
  --sidebar-width-mobile: 18rem;
}

:global(.dark) {
  --sidebar-bg: theme('colors.gray.800');
  --sidebar-border: theme('colors.gray.700');
}

:global(.light) {
  --sidebar-bg: theme('colors.white');
  --sidebar-border: theme('colors.gray.200');
}
</style>
