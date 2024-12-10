<script setup lang="ts">
import type { LayoutItemRequired } from 'grid-layout-plus'
import { computed, ref, useTemplateRef } from 'vue'
import { useMouse, useThrottleFn  } from '@vueuse/core'
import { GridLayout, GridItem } from 'grid-layout-plus'

const dropId = 'drop'
const dragItem = { x: -1, y: -1, w: 2, h: 2, i: '' }

const { x, y } = useMouse({ type: 'screen' })

const layout1 = ref<LayoutItemRequired[]>([
  { x: 0, y: 0, w: 2, h: 2, i: '0' },
  { x: 2, y: 0, w: 2, h: 4, i: '1' }
])

const layout2 = ref<LayoutItemRequired[]>([
  { x: 0, y: 0, w: 2, h: 4, i: '0' },
  { x: 2, y: 0, w: 2, h: 4, i: '1' },
  { x: 0, y: 4, w: 2, h: 4, i: '2' },
  { x: 2, y: 4, w: 2, h: 4, i: '3' }
])

const wrapper1 = useTemplateRef<HTMLDivElement>('wrapper-1')
const gridLayout1 = useTemplateRef<InstanceType<typeof GridLayout>>('grid-layout-1')

const wrapper2 = useTemplateRef<HTMLDivElement>('wrapper-2')
const gridLayout2 = useTemplateRef<InstanceType<typeof GridLayout>>('grid-layout-2')

const wrapper1Rect = computed(() => {
  const rect = wrapper1.value?.getBoundingClientRect()
  return rect ?? null
})

const wrapper2Rect = computed(() => {
  const rect = wrapper2.value?.getBoundingClientRect()
  return rect ?? null
})

const drag = useThrottleFn((id: string) => {
  if (!wrapper1Rect.value || !wrapper2Rect.value) return
  if (!gridLayout1.value || !gridLayout2.value) return

  const mouseInGrid1 = x.value > wrapper1Rect.value.left && 
    x.value < wrapper1Rect.value.right &&
    y.value > wrapper1Rect.value.top &&
    y.value < wrapper1Rect.value.bottom

  const mouseInGrid2 = x.value > wrapper2Rect.value.left && 
    x.value < wrapper2Rect.value.right &&
    y.value > wrapper2Rect.value.top &&
    y.value < wrapper2Rect.value.bottom

  if (mouseInGrid1 && !layout1.value.find(item => item.i === id)) {
    layout1.value.push({
      x: (layout1.value.length * 2) % 12,
      y: layout1.value.length, // puts it at the bottom
      w: 2,
      h: 2,
      i: dropId
    })
  }
})

const dragEnd = () => {
  if (!wrapper1Rect.value || !wrapper2Rect.value) return
  if (!gridLayout1.value || !gridLayout2.value) return
}

</script>

<template>
  <main class="container">
    <p>Premier layout</p>
    <div class="wrapper-1">
      <GridLayout
        ref="grid-layout-1"
        v-model:layout="layout1"
        :col-num="12"
        :row-height="30"
        is-draggable
        is-resizable
        vertical-compact
        use-css-transforms
      >

      </GridLayout>
    </div>
    <p>Second layout</p>
    <div class="wrapper-2">
      <GridLayout
        ref="grid-layout-2"
        v-model:layout="layout2"
        :col-num="12"
        :row-height="30"
        is-draggable
        is-resizable
        vertical-compact
        use-css-transforms
      >

      </GridLayout>
    </div>
    <hr />
    <div
      class="droppable-element"
      draggable="true"
      unselectable="on"
      @drag="drag('item1')"
      @dragend="dragEnd('item1')"
    >
      Droppable Element (Drag me!)
    </div>
    <div
      class="droppable-element"
      draggable="true"
      unselectable="on"
      @drag="drag('item2')"
      @dragend="dragEnd('item2')"
    >
      Droppable Element (Drag me!)
    </div>
  </main>
</template>


<style scoped>
.vgl-layout {
  background-color: #eee;
}

:deep(.vgl-item:not(.vgl-item--placeholder)) {
  background-color: #ccc;
  border: 1px solid black;
}

:deep(.vgl-item--resizing) {
  opacity: 90%;
}

:deep(.vgl-item--static) {
  background-color: #cce;
}

.text {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  margin: auto;
  font-size: 24px;
  text-align: center;
}
</style>