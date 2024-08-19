<script setup>
import { ref, onMounted, computed } from 'vue';
import router from '@/router';
import axios from 'axios';
import TodoList from '@/components/TodoList.vue';

const apiBaseUrl = 'https://todolist-api.hexschool.io';
const nickName = ref('');
const token = ref('');
const tabActiveIndex = ref(0);
const todo = ref('');
const todos = ref([
  {
    id: 1,
    txt: '把冰箱發霉的檸檬拿去丟',
    completed: false
  },
  {
    id: 2,
    txt: '打電話叫媽媽匯款給我',
    completed: false
  },
  {
    id: 3,
    txt: '整理電腦資料夾',
    completed: false
  },
  {
    id: 4,
    txt: '繳電費水費瓦斯費',
    completed: false
  },
  {
    id: 5,
    txt: '約vicky禮拜三泡溫泉',
    completed: false
  },
  {
    id: 6,
    txt: '約ada禮拜四吃晚餐',
    completed: false
  }
]);

const addTodoHandler = () => {
  todos.value.push({
    id: new Date().getTime(),
    txt: todo.value,
    completed: false
  });
};

// 登出
const signOut = async () => {
  try {
    await axios.post(
      `${apiBaseUrl}/users/sign_out`,
      {},
      {
        headers: {
          Authorization: token.value
        }
      }
    );
    token.value = '';
    document.cookie = 'authInfo=;max-age=0;';
    //導到登入頁
    router.push('/login');
  } catch (error) {
    console.log(error);
  }
};

const signOutHandler = () => {
  signOut();
};

const removeTodoHandler = (todo) => {
  todos.value = todos.value.filter((item) => item.id !== todo.id);
};

const incomplete = computed(() => {
  return todos.value.filter((item) => !item.completed);
});

const complete = computed(() => {
  return todos.value.filter((item) => item.completed);
});

onMounted(() => {
  const authInfo = document.cookie
    .split('; ')
    .find((row) => row.startsWith('authInfo='))
    ?.split('=')[1];
  nickName.value = JSON.parse(authInfo).user;
  token.value = JSON.parse(authInfo).token;
});
</script>

<template>
  <!-- ToDo List -->
  <div id="todoListPage" class="bg-half">
    <nav>
      <h1><a href="#">ONLINE TODO LIST</a></h1>
      <ul>
        <li class="todo_sm">
          <a href="#"
            ><span>{{ nickName }} 的代辦</span></a
          >
        </li>
        <li><a href="#" @click.prevent="signOutHandler">登出</a></li>
      </ul>
    </nav>
    <div class="conatiner todoListPage vhContainer">
      <div class="todoList_Content">
        <div class="inputBox">
          <input v-model="todo" type="text" placeholder="請輸入待辦事項" />
          <a href="#" @click.prevent="addTodoHandler">
            <i class="fa fa-plus"></i>
          </a>
        </div>
        <div class="todoList_list">
          <ul class="todoList_tab">
            <li v-for="(item, index) in ['全部', '待完成', '已完成']" :key="item">
              <a
                href="#"
                :class="{ active: tabActiveIndex === index }"
                @click.prevent="tabActiveIndex = index"
                >{{ item }}</a
              >
            </li>
          </ul>
          <div class="todoList_items">
            <template v-if="todos.length <= 0">
              <div>目前尚無待辦事項</div>
            </template>
            <template v-else>
              <!-- 全部 -->
              <TodoList
                v-if="tabActiveIndex === 0"
                :data="todos"
                :remove-todo="removeTodoHandler"
              />
              <!-- 待完成 -->
              <TodoList
                v-else-if="incomplete && tabActiveIndex === 1"
                :data="incomplete"
                status="incomplete"
                :remove-todo="removeTodoHandler"
              />
              <!-- 已完成 -->
              <TodoList
                v-else
                :data="complete"
                status="complete"
                :remove-todo="removeTodoHandler"
              />
            </template>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped></style>
