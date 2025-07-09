<script setup lang="ts">
import { provide, ref, computed, type Ref } from 'vue'
import { breakpointsTailwind, useBreakpoints } from '@vueuse/core'

// Define sidebar state interface
interface SidebarState {
  open: Ref<boolean>
  setOpen: (value: boolean) => void
  isMobile: Ref<boolean>
  openMobile: Ref<boolean>
  setOpenMobile: (value: boolean) => void
}

// Props
interface SidebarProviderProps {
  defaultOpen?: boolean
  open?: boolean
}

const props = withDefaults(defineProps<SidebarProviderProps>(), {
  defaultOpen: true,
  open: undefined,
})

// Emits
const emit = defineEmits<{
  'update:open': [value: boolean]
}>()

// Breakpoints for mobile detection
const breakpoints = useBreakpoints(breakpointsTailwind)
const isMobile = breakpoints.smaller('lg') // < 1024px

// Desktop sidebar state
const internalOpen = ref(props.defaultOpen)
const open = computed({
  get: () => props.open ?? internalOpen.value,
  set: (value: boolean) => {
    if (props.open === undefined) {
      internalOpen.value = value
    }
    emit('update:open', value)
  },
})

// Mobile sidebar state
const openMobile = ref(false)

// Functions
const setOpen = (value: boolean): void => {
  open.value = value
}

const setOpenMobile = (value: boolean): void => {
  openMobile.value = value
}

// Provide sidebar context
const sidebarContext: SidebarState = {
  open,
  setOpen,
  isMobile,
  openMobile,
  setOpenMobile,
}

provide('sidebar', sidebarContext)
</script>

<template>
  <div
    :style="{
      '--sidebar-width': '16rem',
      '--sidebar-width-mobile': '18rem',
    }"
    :class="{
      'sidebar-desktop-open': !isMobile && open,
      'sidebar-mobile-open': isMobile && openMobile,
    }"
  >
    <slot />
  </div>
</template>

<style scoped>
:global(.sidebar-desktop-open) {
  --sidebar-width: 16rem;
}

:global(.sidebar-mobile-open) {
  --sidebar-width-mobile: 18rem;
}
</style>