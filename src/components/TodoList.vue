<script setup>
import { computed } from 'vue';

const props = defineProps({
  data: {
    type: Array,
    required: true
  },
  status: {
    type: String,
    default: 'All'
  },
  deleteTodo: {
    type: Function,
    default: () => {}
  },
  toggleStatus: {
    type: Function,
    default: () => {}
  }
});

const result = computed(() => {
  const txt = props.status === 'complete' ? '個已完成項目' : '個待完成項目';
  if (props.status === 'complete') {
    const completeLength = props.data.filter((item) => item.status).length;
    return `${completeLength} ${txt}`;
  } else {
    const incompleteLength = props.data.filter((item) => !item.status).length;
    return `${incompleteLength} ${txt}`;
  }
});
</script>

<template>
  <ul class="todoList_item">
    <li v-for="item in data" :key="item.id">
      <label class="todoList_label">
        <input
          class="todoList_input"
          type="checkbox"
          v-model="item.status"
          @change="toggleStatus(item.id)"
        />
        <span>{{ item.content }}</span>
      </label>
      <a href="#" @click.prevent="deleteTodo(item.id)">
        <i class="fa fa-times"></i>
      </a>
    </li>
  </ul>
  <div class="todoList_statistics">
    <p>{{ result }}</p>
  </div>
</template>

<style lang="scss" scoped></style>
