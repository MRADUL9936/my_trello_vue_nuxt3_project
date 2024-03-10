<script setup lang="ts">

  import type {Column, Task} from "@/types";
  import {nanoid} from "nanoid";
  import draggable from "vuedraggable";



  const columns = useLocalStorage<Column[]>("trelloBoard", [
    {
      id: nanoid(),
      title: "Not Started",
      tasks: []
    },
    {
      title: "In progress",
      id: nanoid(),
      tasks: []
    },
    {
      title: "Completed",
      id: nanoid(),
      tasks: []
    },
  ])
   const control = useKeyModifier("Control")

</script>

<template>
  <section class="flex items-start  gap-4">
    <div
      v-for="column in columns"
      :key="column.id"
      class="column bg-gray-200 p-5 rounded min-w-[30%]"
    >
      <header class="font-bold mb-2">
        <span>{{ column.title }} <span class="px-5">{{ column.tasks.length }}</span> </span>
      </header>
      <draggable
        v-model="column.tasks"
        :group="{ name: 'tasks', pull: control ? 'clone' : true}"
        item-key="id"
        :animation="150"
        handle=".drag-handle"
      >
        <template #item="{ element: task } : { element: Task }">
          <div>
            <TrelloBoardTask
              :task="task"
              @delete="column.tasks = column.tasks.filter((t) => t.id !== $event)"
            />
          </div>
        </template>
      </draggable>
      <footer>
        <NewTask @add="column.tasks.push($event)" />
      </footer>
    </div>
  </section>
</template>

