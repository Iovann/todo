<template>
  <div class="flex items-center gap-2 w-full">
    <input
      type="checkbox"
      :checked="task.completed"
      @change="updateTask(task.id, !task.completed)"
      class="w-4 h-4 rounded border-gray-300"
      :id="`task-${task.id}`"
    />
    <label
      :for="`task-${task.id}`"
      class="flex-grow cursor-pointer"
      :class="{ 'line-through opacity-50': task.completed }"
    >
      {{ task.text }}
    </label>
    <button @click="deleteTask(task.id)" class="text-red-500 hover:text-red-700 transition-colors">
      <TrashIcon />
    </button>
  </div>
</template>

<script setup lang="ts">
import TrashIcon from './icons/TrashIcon.vue'
const props = defineProps<{
  task: {
    id: number
    text: string
    completed: boolean
  }
}>()

const emit = defineEmits<{
  (e: 'delete', id: number): void
  (e: 'update', id: number, completed: boolean): void
}>()

const updateTask = (id: number, completed: boolean) => {
  emit('update', id, completed)
}

const deleteTask = (id: number) => {
  emit('delete', id)
}
</script>
