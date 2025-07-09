<script setup lang="ts">
import { cn } from '../../lib/utils'

interface SidebarFooterProps {
  class?: string
}

const props = defineProps<SidebarFooterProps>()
</script>

<template>
  <div :class="cn('flex flex-col gap-2 p-2', props.class)">
    <slot />
  </div>
</template>