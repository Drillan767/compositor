<script setup lang="ts">
import type { LayoutItem } from 'grid-layout-plus'
import { computed, ref, useTemplateRef, onBeforeUpdate, watch } from 'vue'
import { useMouse, useThrottleFn  } from '@vueuse/core'
import { GridLayout, GridItem } from 'grid-layout-plus'
import CompositionPart from './components/CompositionPart.vue'

interface POSLayout {
    name: string
    layout: LayoutItem[]
}

const { x, y } = useMouse()

const pocLayouts = ref<POSLayout[]>([
    {
        name: 'Part 1',
        layout: [
            { x: 0, y: 0, w: 1, h: 1, i: '0' },
            { x: 1, y: 0, w: 1, h: 1, i: '1' }
        ]
    },
    {
        name: 'Part 2',
        layout: [
            { x: 0, y: 0, w: 1, h: 1, i: '0' },
            { x: 1, y: 0, w: 1, h: 1, i: '1' },
            { x: 2, y: 0, w: 1, h: 1, i: '2' },
            { x: 3, y: 0, w: 1, h: 1, i: '3' },
            { x: 4, y: 0, w: 1, h: 1, i: '4' }
        ]
    }
])

const compositionParts = ref<InstanceType<typeof CompositionPart>[]>([])

const checkDropTarget = (dropX: number, dropY: number) => {
    /* if (!compositionParts.value) return false
    for (const partRef of compositionParts.value) {
        //const bounds = partRef.boundaries
        if (!bounds) continue

        if (
            dropX >= bounds.left && 
            dropX <= bounds.right && 
            dropY >= bounds.top && 
            dropY <= bounds.bottom
        ) {
            return partRef // or return the component/index if you need to identify which one
        }
    }
    return false */
}

const drag = () => useThrottleFn(() => {

})

const dragEnd = () => {
    /* if (!compositionParts.value) return
    for (const partRef of compositionParts.value) {
        const bounds = partRef.boundaries
        if (!bounds) continue
        if (checkDropTarget(x.value, y.value)) {
            console.log('drop')
        }
    } */
}

const updateParts = (el: any | null, index: number) => {
    if (!el) return
    compositionParts.value[index] = el
}

onBeforeUpdate(() => {
    compositionParts.value = []
})

watch(compositionParts, (parts) => {
    if (!parts || !parts.length) return

    const hoveredPart = parts.findIndex(part => part.isHovered)
    if (hoveredPart !== -1) {
        console.log('hovered part', hoveredPart)
    }
})

</script>

<template>
    <main class="container">
        <div class="test-wrapper">
            <CompositionPart
                v-for="(layout, index) in pocLayouts"
                :key="layout.name"
                :ref="el => updateParts(el, index)"
                v-model="layout.layout"
                :x
                :y
            />
        </div>

        <div
            class="droppable-element"
            draggable="true"
            unselectable="on"
            @drag="drag"
            @dragend="dragEnd"
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

.test-wrapper {
    display: flex;
    gap: 10px;
    max-width: 1700px;
    overflow-x: scroll;
    overflow-y: hidden;
}

.droppable-element {
  width: 150px;
  padding: 10px;
  margin: 10px 0;
  text-align: center;
  background-color: #fdd;
  border: 1px solid black;
}
</style>