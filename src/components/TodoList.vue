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
  removeTodo: {
    type: Function,
    default: () => {}
  }
});

const result = computed(() => {
  const txt = props.status === 'incomplete' ? '個未完成項目' : '個已完成項目';
  if (props.status === 'incomplete') {
    const incompleteLength = props.data.filter((item) => !item.completed).length;
    return `${incompleteLength} ${txt}`;
  } else {
    const completeLength = props.data.filter((item) => item.completed).length;
    return `${completeLength} ${txt}`;
  }
});
</script>

<template>
  <ul class="todoList_item">
    <li v-for="item in data" :key="item.id">
      <label class="todoList_label">
        <input class="todoList_input" type="checkbox" v-model="item.completed" />
        <span>{{ item.txt }}</span>
      </label>
      <a href="#" @click.prevent="removeTodo(item)">
        <i class="fa fa-times"></i>
      </a>
    </li>
  </ul>
  <div class="todoList_statistics">
    <p>{{ result }}</p>
  </div>
</template>

<style lang="scss" scoped></style>
