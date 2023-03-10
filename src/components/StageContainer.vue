<script setup lang='ts'>
import { ref, defineEmits, watch, onMounted } from 'vue'
import { Stage } from 'src/models/board'

import ItemContainer from './ItemContainer.vue'
import EditableValue from './base/EditableValue.vue'
import AdditionPlaceholder from './base/AdditionPlaceholder.vue'

const props = defineProps<{ value: Stage, index: number }>()
const emit = defineEmits<{
  (eventName: 'add-item') : void,
  (eventName: 'open-item', stageIndex: number) : void,
  (eventName: 'move-item', stageIndex: number, itemId: string) : void,
  (eventName: 'save-name', name: string) : void
}>()

const stageName = ref(props.value.name)
watch(stageName, () => save())

function handleDrop(e: DragEvent) {
  var itemId = e.dataTransfer?.getData('draggedItemId')
  if (itemId) emit('move-item', props.index, itemId)
}

function save() {
  stageName.value.length > 0 ? stageName.value : props.value.name
  emit('save-name', stageName.value)
}
</script>

<template>
  <div
    @drop="handleDrop"
    @dragenter.prevent @dragover.prevent
    class="grow min-w-min max-w-xs overflow-y-scroll snap-y scroll-mb-6
          no-scrollbar p-4 pt-0 rounded-lg shadow-lg"
  >
    <div class="h-full">
      <h2 class="text-center font-bold sticky top-0 py-4 mb-4 -mx-4
          border-b-2 bg-gray-light text-yellow shadow-md z-20" >
        <editable-value label="">
          <template #display>
            <span>{{stageName}}</span>
          </template>
          <template #edit>
            <input
              v-model="stageName"
              class="w-full text-center -mx-2"
              maxlength="240"
            />
          </template>
        </editable-value>
      </h2>

      <div class="grid grid-flow-row gap-1 auto-rows-max">
        <item-container
          v-for="item, i in value.items"
          :key="'item-'+ item.name + item.created"
          :value="item"
          @click="$emit('open-item', i)"
        />
        <addition-placeholder @click="$emit('add-item')" />
      </div>
    </div>
  </div>
</template>

