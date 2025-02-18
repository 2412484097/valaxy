<script lang="ts" setup>
import { onMounted, ref } from 'vue'
import { useI18n } from 'vue-i18n'
import { onContentUpdated, runContentUpdated, useAplayer, useCodePen, useCopyCode, useMediumZoom, wrapTable } from 'valaxy'
import type { Post } from 'valaxy'
import { useVanillaLazyLoad } from '../composables/features/vanilla-lazyload'

const props = defineProps<{
  frontmatter: Post
  excerpt?: string
}>()

const { t } = useI18n()

const contentRef = ref()
onContentUpdated(() => {
  wrapTable(contentRef.value)
})

onMounted(() => {
  runContentUpdated()
})

// widgets
if (props.frontmatter.aplayer)
  useAplayer()

if (props.frontmatter.codepen)
  useCodePen()

useCopyCode()

if (typeof props.frontmatter.medium_zoom === 'undefined' || props.frontmatter.medium_zoom)
  useMediumZoom()

useVanillaLazyLoad()
</script>

<template>
  <article v-if="$slots.default" :class="frontmatter.markdown !== false && 'markdown-body'">
    <template v-if="frontmatter.encryptedContent">
      <ValaxyDecrypt :encrypted-content="frontmatter.encryptedContent" />
    </template>
    <slot v-else ref="contentRef" @vue:updated="runContentUpdated" />

    <div v-if="frontmatter.url" text="center">
      <a

        :href="frontmatter.url"
        class="post-link-btn shadow hover:shadow-md"
        rounded
        target="_blank"
        m="b-4"
      >
        {{ t('post.view_link') }}
      </a>
    </div>

    <slot v-if="frontmatter.end !== undefined" name="end">
      <div m="y-4" class="end flex justify-center items-center">
        <hr class="line inline-flex" w="full" m="!y-2">
        <span p="x-4" font="serif bold" class="whitespace-nowrap">
          {{ frontmatter.end ? 'Q.E.D.' : 'To Be Continued.' }}
        </span>
        <hr class="line inline-flex" w="full" m="!y-2">
      </div>
    </slot>
  </article>
</template>

<style lang="scss">
.medium-zoom-overlay,
.medium-zoom-image--opened {
  z-index: 999;
}
</style>
