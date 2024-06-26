<script setup lang="ts">
import { Message } from '@arco-design/web-vue'
import { useCollectionSetStore } from '@renderer/store/collection-set'
import { useDrawingStore } from '@renderer/store/drawing'
import { useSystemStore } from '@renderer/store/system'
import { nowTimestamp } from '@renderer/utils/date-util'
import { downloadFile } from '@renderer/utils/download-util'
import { randomUUID } from '@renderer/utils/id-util'
import { copyObj } from '@renderer/utils/object-util'
import { reactive, toRefs } from 'vue'
import { useI18n } from 'vue-i18n'

const collectionSetStore = useCollectionSetStore()
const { t } = useI18n()

// store
const systemStore = useSystemStore()
const drawingStore = useDrawingStore()

// 数据绑定
const data = reactive({
  imageListIndex: 0
})
const { imageListIndex } = toRefs(data)

// 收藏
const collectImage = () => {
  const collectionItem: CollectionItem = {
    id: randomUUID(),
    type: 'image',
    image: {
      ...copyObj(drawingStore.getCurrentTask),
      imageList: [drawingStore.getCurrentTask.imageList[data.imageListIndex]]
    },
    createTime: nowTimestamp()
  }
  collectionSetStore.collectionItemList.unshift(collectionItem)
  Message.success(t('chatWindow.collectSuccess'))
}
</script>

<template>
  <a-spin :loading="systemStore.aiDrawingLoading" class="ai-drawing-img-current" tip="">
    <template v-if="drawingStore.getCurrentTask.imageList.length > 0">
      <a-image :src="`file://${drawingStore.getCurrentTask.imageList[imageListIndex]}`" show-loader>
        <template #preview-actions>
          <a-image-preview-action
            :name="$t('common.download')"
            @click="
              downloadFile(
                `file://${drawingStore.getCurrentTask.imageList[imageListIndex]}`,
                `img-${drawingStore.getCurrentTask.id}-${imageListIndex}.png`
              )
            "
          >
            <icon-download />
          </a-image-preview-action>
          <a-image-preview-action :name="$t('common.collect')" @click="collectImage()">
            <icon-common />
          </a-image-preview-action>
        </template>
      </a-image>
      <div v-if="drawingStore.getCurrentTask.imageList.length > 1" class="image-index-list">
        <a-button
          v-for="i in drawingStore.getCurrentTask.imageList.length"
          :key="i"
          size="mini"
          shape="circle"
          :type="i === imageListIndex + 1 ? 'primary' : undefined"
          @click="imageListIndex = i - 1"
          >{{ i }}</a-button
        >
      </div>
    </template>
    <div v-else class="ai-drawing-img-default"></div>
  </a-spin>
</template>

<style scoped lang="less">
.ai-drawing-img-current {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  box-sizing: border-box;
  padding: 15px;

  :deep(.arco-image) {
    height: 100%;
    aspect-ratio: 1 / 1;
    border-radius: 0;

    .arco-image-img {
      height: 100%;
      width: 100%;
      object-fit: contain;
    }
  }

  .image-index-list {
    margin-top: 15px;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 10px;
  }

  .ai-drawing-img-default {
    height: 100%;
    aspect-ratio: 1 / 1;
    background-color: var(--color-fill-3);
  }
}
</style>
