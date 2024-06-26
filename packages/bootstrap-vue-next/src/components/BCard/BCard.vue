<template>
  <component :is="props.tag" class="card" :class="computedClasses">
    <ReusableImg.define>
      <slot name="img">
        <BCardImg v-if="props.imgSrc" v-bind="imgAttr" />
      </slot>
    </ReusableImg.define>

    <ReusableImg.reuse v-if="!props.imgBottom" />
    <BCardHeader
      v-if="props.header || hasHeaderSlot || props.headerHtml"
      :bg-variant="props.headerBgVariant"
      :variant="props.headerVariant"
      :border-variant="props.headerBorderVariant"
      :html="props.headerHtml"
      :tag="props.headerTag"
      :text-variant="props.headerTextVariant"
      :class="props.headerClass"
    >
      <slot name="header">
        {{ props.header }}
      </slot>
    </BCardHeader>
    <BCardBody
      v-if="!props.noBody"
      :overlay="props.overlay"
      :bg-variant="props.bodyBgVariant"
      :tag="props.bodyTag"
      :text-variant="props.bodyTextVariant"
      :subtitle="props.subtitle"
      :subtitle-tag="props.subtitleTag"
      :subtitle-text-variant="props.subtitleTextVariant"
      :title="props.title"
      :title-tag="props.titleTag"
      :class="props.bodyClass"
    >
      <slot>
        {{ props.bodyText }}
      </slot>
    </BCardBody>
    <slot v-else>
      {{ props.bodyText }}
    </slot>
    <BCardFooter
      v-if="props.footer || hasFooterSlot || props.footerHtml"
      :bg-variant="props.footerBgVariant"
      :border-variant="props.footerBorderVariant"
      :variant="props.footerVariant"
      :html="props.footerHtml"
      :tag="props.footerTag"
      :text-variant="props.footerTextVariant"
      :class="props.footerClass"
    >
      <slot name="footer">
        {{ props.footer }}
      </slot>
    </BCardFooter>
    <ReusableImg.reuse v-if="props.imgBottom" />
  </component>
</template>

<script setup lang="ts">
import type {BCardProps} from '../../types'
import {isEmptySlot} from '../../utils'
import {computed, toRef} from 'vue'
import {useColorVariantClasses, useDefaults} from '../../composables'
import BCardImg from './BCardImg.vue'
import BCardHeader from './BCardHeader.vue'
import BCardBody from './BCardBody.vue'
import BCardFooter from './BCardFooter.vue'
import {createReusableTemplate} from '@vueuse/core'

const _props = withDefaults(defineProps<BCardProps>(), {
  align: undefined,
  bodyBgVariant: undefined,
  bodyClass: undefined,
  bodyTag: 'div',
  bodyText: '',
  bodyTextVariant: undefined,
  borderVariant: null,
  footer: undefined,
  footerBgVariant: undefined,
  footerBorderVariant: undefined,
  footerClass: undefined,
  footerHtml: '',
  footerTag: 'div',
  footerTextVariant: undefined,
  footerVariant: null,
  header: undefined,
  headerBgVariant: undefined,
  headerBorderVariant: undefined,
  headerClass: undefined,
  headerHtml: '',
  headerTag: 'div',
  headerTextVariant: undefined,
  headerVariant: null,
  imgAlt: undefined,
  imgBottom: false,
  imgEnd: false,
  imgHeight: undefined,
  imgSrc: undefined,
  imgStart: false,
  imgTop: false,
  imgWidth: undefined,
  noBody: false,
  overlay: false,
  subtitle: undefined,
  subtitleTag: 'h6',
  subtitleTextVariant: 'body-secondary',
  tag: 'div',
  title: undefined,
  titleTag: 'h4',
  // ColorExtendables props
  bgVariant: null,
  textVariant: null,
  variant: null,
  // End ColorExtendables props
})
const props = useDefaults(_props, 'BCard')

const slots = defineSlots<{
  // eslint-disable-next-line @typescript-eslint/no-explicit-any
  default?: (props: Record<string, never>) => any
  // eslint-disable-next-line @typescript-eslint/no-explicit-any
  footer?: (props: Record<string, never>) => any
  // eslint-disable-next-line @typescript-eslint/no-explicit-any
  header?: (props: Record<string, never>) => any
  // eslint-disable-next-line @typescript-eslint/no-explicit-any
  img?: (props: Record<string, never>) => any
}>()

const hasHeaderSlot = toRef(() => !isEmptySlot(slots.header))
const hasFooterSlot = toRef(() => !isEmptySlot(slots.footer))

const resolvedBackgroundClasses = useColorVariantClasses(props)

const computedClasses = computed(() => [
  resolvedBackgroundClasses.value,
  {
    [`text-${props.align}`]: props.align !== undefined,
    [`border-${props.borderVariant}`]: props.borderVariant !== null,
    'flex-row': props.imgStart,
    'flex-row-reverse': props.imgEnd,
  },
])

const imgAttr = computed(() => ({
  src: props.imgSrc,
  alt: props.imgAlt,
  height: props.imgHeight,
  width: props.imgWidth,
  bottom: props.imgBottom,
  end: props.imgEnd,
  start: props.imgStart,
  top: props.imgTop,
}))

const ReusableImg = createReusableTemplate()
</script>
