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
          <TodoItem :task="task" @delete="deleteTask" @update="updateTask" />
        </div>
      </div>
      <div v-else class="text-center text-gray-500">Aucune tâche</div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import TodoItem from './TodoItem.vue'

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

const updateTask = (id: number, completed: boolean): void => {
  const task = tasks.value.find((elem) => elem.id === id)
  if (task) {
    task.completed = completed
  }
}
</script>
