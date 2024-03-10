<script setup lang="ts">
  import { ref, defineProps, defineEmits } from 'vue';
  import { ID, Task } from '@/types';
  import { format } from 'date-fns';

  const props = defineProps<{
    task: Task;
  }>();

  const emit = defineEmits<{
    (e: "delete", payload: ID): void;
  }>();

  const isEditing = ref(false);
  const editedTitle = ref(props.task.title);
  const editedDescription = ref(props.task.description);

  const startEditing = () => {
    isEditing.value = true;
  };

  const saveChanges = () => {
    props.task.title = editedTitle.value;
    props.task.description = editedDescription.value;
    isEditing.value = false;
  };

  const cancelEditing = () => {
    // Cancel editing and revert changes
    isEditing.value = false;
    editedTitle.value = props.task.title;
    editedDescription.value = props.task.description;
  };

  const deleteTask = () => {
    emit("delete", props.task.id);
  };

  const onKeyPress = (e: KeyboardEvent) => {
    if (e.key === 'Enter') {
      saveChanges();
    }
  };

</script>

<template>
  <div
    :title="format(new Date(task.createdAt), 'dd/MM/yyyy')"
    class="task bg-white p-2 mb-2 rounded shadow-sm max-w-[3000px] flex relative"
  >
    <DragHandle class="pr-2"/>
    <span v-if="!isEditing" @click="startEditing">
      {{ task.title }}
    </span>
    <div v-if="isEditing" class="edit-modal">
      <div class="modal-header">
        <h3 class="header">Edit Task</h3>
        <button class="cancel_button" @click="cancelEditing">X</button>
      </div>
      <label>Title:</label>
      <input class="title_input" v-model="editedTitle" @keypress="onKeyPress" />
      <label>Description:</label>
      <textarea class="description" v-model="editedDescription"></textarea>
      <div class="scbuttons">
        <button @click="saveChanges">Save</button>
        <button @click="cancelEditing">Cancel</button>
        <button @click="deleteTask" class="delete-btn">Delete</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
  h3 {
    color: black;
    font-weight: bold;
    text-align: center;
    font-size: 18px;
  }

  label {
    font-weight: bold;
  }

  .scbuttons {
    display: flex;
    gap: 10px;
    justify-content: space-between;
  }

  .title_input,
  .description {
    border: 1px solid gray;
    padding: 5px;
    width: 100%;
  }

  .edit-modal {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: white;
    padding: 20px;
    border: 1px solid #ccc;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    z-index: 10; /* Adjust the z-index as needed */
    width: 500px; /* Adjust the width as needed */
  }

  .edit-modal label {
    display: block;
    margin-bottom: 5px;
  }

  .modal-header {
    display: flex;
    align-items: center;
    margin-bottom: 10px;
    justify-content: space-between;
  }

  .modal-header.header {
    display: flex;
    justify-self: center;
  }

  .delete-btn {
    color: red; /* Add your delete button styling */
    cursor: pointer;
    text-align: right;
  }
</style>
