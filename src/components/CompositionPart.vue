<script setup lang="ts">
import { computed, ref, useTemplateRef } from 'vue'
import { GridLayout, type LayoutItem} from 'grid-layout-plus'

interface Props {
    x: number
    y: number
    items: LayoutItem[]
}

const { x, y } = defineProps<Props>()

const layout = defineModel<LayoutItem[]>({ required: true })

const wrapper = useTemplateRef<HTMLDivElement>('wrapper')



</script>

<template>
   <div ref="wrapper">
        <GridLayout
            v-model:layout="layout"
            :max-rows="5"
            :auto-size="false"
        >

        </GridLayout>
   </div>
</template>
