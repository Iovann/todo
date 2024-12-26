<template>
  <div class="min-w-96 max-w-md mx-auto bg-white rounded-xl shadow-md overflow-hidden">
    <div class="relative">
      <div class="bg-violet-200 px-6 py-8 rounded-t-xl mb-6">
        <div class="font-bold text-white flex gap-2 items-end">
          <div class="text-5xl">{{ completedTasks }}</div>

          <div class="flex flex-col">
            <span class="font-medium">Tasks</span>
            <span class="text-xl">/{{ tasks.length }}</span>
          </div>
        </div>
      </div>
      <button
        v-if="!showInput"
        @click="showInput = true"
        class="mt-4 w-14 h-14 bg-blue-500 rounded-full shadow-lg hover:bg-blue-600 transition-colors absolute right-4 -bottom-7"
      ></button>
    </div>

    <!-- champ de saisie -->
    <div class="px-4 pb-6">
      <div v-if="showInput" class="mt-4 flex gap-2">
        <input
          v-model="newTask"
          @keyup.enter="addTask"
          type="text"
          class="flex-1 border rounded-md px-3 py-2"
          placeholder="New task"
        />
        <button
          @click="addTask"
          class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600"
        >
          Add
        </button>
      </div>

      <!-- Filtre -->
      <div class="flex border-b mb-4">
        <button
          v-for="tab in tabs"
          :key="tab.id"
          @click="currentTab = tab.id"
          :class="['px-4 py-2', currentTab === tab.id ? 'border-b-2 border-blue-500' : '']"
        >
          {{ tab.name }}
        </button>
      </div>

      <!-- Liste des tâches -->
      <div class="space-y-2" v-if="filteredTasks.length > 0">
        <div v-for="task in filteredTasks" :key="task.id" class="flex items-center gap-2">
          <input type="checkbox" v-model="task.completed" class="w-4 h-4 rounded border-gray-300" />
          <span :class="{ 'line-through opacity-50': task.completed }">{{ task.text }}</span>
          <button
            @click="deleteTask(task.id)"
            class="ml-auto opacity-100 text-red-500 hover:text-red-700 transition-opacity"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="h-5 w-5"
              viewBox="0 0 20 20"
              fill="currentColor"
            >
              <path
                fill-rule="evenodd"
                d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z"
                clip-rule="evenodd"
              />
            </svg>
          </button>
        </div>
      </div>
      <div v-else class="text-center text-gray-500">Aucune tâche</div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

interface Task {
  id: number
  text: string
  completed: boolean
}

interface Tab {
  id: 'all' | 'completed' | 'active'
  name: string
}

const tasks = ref<Task[]>([])
const newTask = ref<string>('')
const showInput = ref<boolean>(false)
const currentTab = ref<Tab['id']>('all')

const tabs: Tab[] = [
  { id: 'all', name: 'Tout' },
  { id: 'completed', name: 'Faits' },
  { id: 'active', name: 'Non Faits' },
]

const filteredTasks = computed<Task[]>(() => {
  switch (currentTab.value) {
    case 'completed':
      return tasks.value.filter((task) => task.completed)
    case 'active':
      return tasks.value.filter((task) => !task.completed)
    default:
      return tasks.value
  }
})

const completedTasks = computed<number>(() => {
  return tasks.value.filter((task) => task.completed).length
})

const addTask = (): void => {
  if (newTask.value.trim()) {
    const newTaskItem: Task = {
      id: Date.now(),
      text: newTask.value,
      completed: false,
    }
    tasks.value.push(newTaskItem)
    newTask.value = ''
    showInput.value = false
  }
}

const deleteTask = (taskId: number): void => {
  tasks.value = tasks.value.filter((task) => task.id !== taskId)
}
</script>
