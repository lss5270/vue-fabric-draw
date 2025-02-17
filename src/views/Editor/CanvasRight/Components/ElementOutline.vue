<template>
  <div class="element-outline">
    <div class="row">
      <div style="flex: 2;"><b>启用边框：</b></div>
      <div class="switch-wrapper" style="flex: 3;">
        <el-switch v-model="hasOutline" @change="toggleOutline(hasOutline)"></el-switch>
      </div>
    </div>
    <template v-if="hasOutline">
      <div class="row">
        <div style="flex: 2;">边框样式：</div>
        <el-select style="flex: 3;" v-model="outlineStyle" @change="changeOutlineStyle">
          <el-option :value="0" label="实线边框"></el-option>
          <el-option :value="1" label="虚线边框"></el-option>
        </el-select>
      </div>
      <div class="row">
        <div style="flex: 2;">边框颜色：</div>
        <el-popover trigger="click" width="265">
          <template #reference>
            <ColorButton :color="handleElement.stroke" style="flex: 3;" />
          </template>
          <ColorPicker :modelValue="handleElement.stroke" @update:modelValue="color => updateOutlineColor(color)"/>
        </el-popover>
      </div>
      <div class="row">
        <div style="flex: 2;">边框粗细：</div>
        <el-input-number style="flex: 3;" v-model="handleElement.strokeWidth"></el-input-number>
      </div>
    </template>
  </div>
</template>

<script lang="ts" setup>
import { ref, watch, computed } from 'vue'
import { storeToRefs } from 'pinia'
import { useMainStore } from '@/store'
import { CanvasElement } from '@/types/canvas'
import useCanvas from '@/views/Canvas/useCanvas'

const [ canvas ] = useCanvas()
const { canvasObject } = storeToRefs(useMainStore())
const handleElement = computed(() => {
  return canvasObject.value as CanvasElement
})
const hasOutline = ref(false)
const outlineStyle = ref(0)

watch(handleElement, () => {
  if (!handleElement.value) return
  hasOutline.value = handleElement.value.stroke ? true : false
})

const toggleOutline = (status: boolean) => {
  if (!handleElement.value) return
  if (status) {
    handleElement.value.stroke = '#555'
    handleElement.value.strokeWidth = 1
  }
  else {
    handleElement.value.stroke = ''
  }
  canvas.renderAll()
}

const changeOutlineStyle = () => {
  if (!handleElement.value) return
  handleElement.value.strokeDashArray = null
  if (outlineStyle.value === 1) {
    handleElement.value.strokeDashArray = [6, 6]
  }
  canvas.renderAll()
}

const updateOutlineColor = (color: string) => {
  handleElement.value.stroke = color
  canvas.renderAll()
}
</script>

<style lang="scss" scoped>
.row {
  width: 100%;
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}
.switch-wrapper {
  text-align: right;
}
</style>