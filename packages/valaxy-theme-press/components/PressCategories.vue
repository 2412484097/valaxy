<script lang="ts" setup>
import { useThemeConfig } from 'valaxy'
import type { Categories } from 'valaxy'
import { computed, ref } from 'vue'
import type { PressTheme } from '../types'

const props = withDefaults(defineProps<{
  categories: Categories
  /**
   * 当前层级
   */
  level?: number
  displayCategory?: (category: string) => void
  collapsable?: boolean
}>(), {
  level: 0,
  collapsable: true,
})

const collapsable = ref(props.collapsable)

const themeConfig = useThemeConfig<PressTheme.Config>()
const sidebar = computed(() => themeConfig.value.sidebar)

function getCategoryByName(name: string) {
  return props.categories.find(c => c.name === name)
}
</script>

<template>
  <ul v-for="item in sidebar" :key="item" class="category-list">
    <PressCategory
      v-if="getCategoryByName(item)"
      :category="getCategoryByName(item)"
      :level="level + 1"
      :display-category="displayCategory"
      :collapsable="collapsable"
    />
  </ul>
</template>

<style lang="scss">
.category-list {
  &:first-child {
    .category-list-item {
      border-top: 0px;
    }
  }
}
.post-list-item {
  a {
    color: var(--va-c-text-light);
    transition: all 0.2s;

    &:hover {
      color: var(--va-c-primary);
    }

    &.active {
      color: var(--va-c-primary);
    }
  }
}

.category-list-item {
  .folder-action {
    &:hover {
      color: var(--va-c-primary);
    }
  }
}

.category-list+.category-list {
  margin-top: 1rem;
}
</style>
