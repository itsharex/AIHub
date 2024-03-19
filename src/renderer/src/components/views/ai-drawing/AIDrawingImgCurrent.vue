<script setup lang="ts">
import { useDrawingStore } from '@renderer/store/drawing'
import { useSystemStore } from '@renderer/store/system'
import { downloadFile } from '@renderer/utils/download-util'

// store
const systemStore = useSystemStore()
const drawingStore = useDrawingStore()
</script>

<template>
  <div class="ai-drawing-img-current">
    <a-spin :loading="systemStore.aiDrawingLoading" tip="">
      <a-image
        v-if="drawingStore.getCurrentTask.imageList[0]"
        width="500"
        height="500"
        :src="`file://${drawingStore.getCurrentTask.imageList[0]}`"
        show-loader
        fit="cover"
      >
        <template #preview-actions>
          <a-image-preview-action
            :name="$t('common.download')"
            @click="
              downloadFile(
                `file://${drawingStore.getCurrentTask.imageList[0]}`,
                `img-${drawingStore.getCurrentTask.id}.png`
              )
            "
            ><icon-download
          /></a-image-preview-action>
        </template>
      </a-image>
      <div v-else class="ai-drawing-img-default"></div>
    </a-spin>
  </div>
</template>

<style scoped lang="less">
.ai-drawing-img-current {
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;

  :deep(.arco-image) {
    border-radius: 0;
  }

  .ai-drawing-img-default {
    width: 500px;
    height: 500px;
    background-color: var(--color-fill-3);
  }
}
</style>