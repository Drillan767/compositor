<script setup lang="ts">
import { computed, ref, useTemplateRef } from 'vue'
import { GridLayout, type LayoutItem} from 'grid-layout-plus'

interface Props {
    x: number
    y: number
}

const nbCols = ref(5)

const addCol = () => {
    nbCols.value++
}

const { x, y } = defineProps<Props>()

const layout = defineModel<LayoutItem[]>({ required: true })

const wrapper = useTemplateRef<HTMLDivElement>('wrapper')

const isHovered = computed(() => {
    const bounds = wrapper.value?.getBoundingClientRect()
    return bounds &&
    x >= bounds.left &&
    x <= bounds.right &&
    y >= bounds.top &&
    y <= bounds.bottom || false
})

defineExpose({
    isHovered
})

</script>

<template>
   <div
        ref="wrapper"
        class="layout"
    >
        <p>Partie {{ x }} {{ y }}</p>
        <button @click="addCol">+</button>
        <GridLayout
            v-model:layout="layout"
            :max-rows="5"
            v-model:col-num="nbCols"
            :style="`width: calc(550px + ${nbCols * 50}px)`"
            :row-height="80"
            :auto-size="false"
        >

        </GridLayout>
   </div>
</template>

<style scoped>
.layout {
    height: 550px;
    background-color: aqua;
    min-width: 550px;
}
</style>