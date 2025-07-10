<script setup lang="ts">
import { cn } from '../../../lib/utils'

interface SidebarHeaderProps {
  class?: string
}

const props = defineProps<SidebarHeaderProps>()
</script>

<template>
  <div :class="cn('flex flex-col gap-2 p-2', props.class)">
    <slot />
  </div>
</template>
