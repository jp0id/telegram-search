<script setup lang="ts">
import { computed } from 'vue'
import { cn } from '../../lib/utils'

interface SidebarMenuButtonProps {
  asChild?: boolean
  isActive?: boolean
  variant?: 'default' | 'outline'
  size?: 'default' | 'sm' | 'lg'
  class?: string
}

const props = withDefaults(defineProps<SidebarMenuButtonProps>(), {
  asChild: false,
  isActive: false,
  variant: 'default',
  size: 'default',
})

const buttonClasses = computed(() => {
  return cn(
    'peer/menu-button flex w-full items-center gap-2 overflow-hidden rounded-md p-2 text-left text-sm outline-none transition-colors hover:bg-accent hover:text-accent-foreground focus-visible:ring-2 focus-visible:ring-ring active:bg-accent active:text-accent-foreground disabled:pointer-events-none disabled:opacity-50 aria-disabled:pointer-events-none aria-disabled:opacity-50 [&>span:last-child]:truncate [&>svg]:size-4 [&>svg]:shrink-0',
    props.variant === 'outline' &&
      'border border-border bg-background shadow-sm hover:bg-accent hover:text-accent-foreground',
    props.size === 'sm' && 'text-xs p-1',
    props.size === 'lg' && 'text-base p-3',
    props.isActive && 'bg-accent font-medium text-accent-foreground',
    props.class
  )
})
</script>

<template>
  <div
    v-if="asChild"
    :class="buttonClasses"
    :data-active="isActive"
    role="button"
    tabindex="0"
  >
    <slot />
  </div>
  
  <button
    v-else
    :class="buttonClasses"
    :data-active="isActive"
    type="button"
  >
    <slot />
  </button>
</template>