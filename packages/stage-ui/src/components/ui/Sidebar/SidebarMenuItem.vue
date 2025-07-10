<script setup lang="ts">
import { cn } from '../../../lib/utils'

interface SidebarMenuItemProps {
  class?: string
}

const props = defineProps<SidebarMenuItemProps>()
</script>

<template>
  <li :class="cn('group/menu-item relative', props.class)">
    <slot />
  </li>
</template>
