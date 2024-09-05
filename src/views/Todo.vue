<script setup>
import { ref, onMounted, computed } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';
import TodoList from '@/components/TodoList.vue';
import Loading from '@/components/Loading.vue';

const router = useRouter();
const apiBaseUrl = 'https://todolist-api.hexschool.io';

const isLoading = ref(false);
// Todo list
const todos = ref([]);
const newTodo = ref('');
const nickName = ref('');
const token = ref('');
const tabActiveIndex = ref(0);

// 獲取代辦清單
const getTodos = async () => {
  const response = await axios.get(`${apiBaseUrl}/todos`, {
    headers: {
      Authorization: token.value
    }
  });
  todos.value = response.data.data;
};

// 新增
const addTodo = async () => {
  if (!newTodo.value) {
    alert('請輸入待辦事項');
    return;
  }
  const todo = {
    content: newTodo.value
  };
  try {
    isLoading.value = true;
    await axios.post(`${apiBaseUrl}/todos`, todo, {
      headers: {
        Authorization: token.value
      }
    });
    isLoading.value = false;
    newTodo.value = '';
    getTodos();
  } catch (error) {
    console.log(error);
  }
};

// 刪除
const deleteTodo = async (id) => {
  try {
    isLoading.value = true;
    await axios.delete(`${apiBaseUrl}/todos/${id}`, {
      headers: {
        Authorization: token.value
      }
    });
    isLoading.value = false;
    getTodos();
  } catch (error) {
    console.log(error);
  }
};

// 切換狀態
const toggleStatus = async (id) => {
  try {
    isLoading.value = true;
    await axios.patch(
      `${apiBaseUrl}/todos/${id}/toggle`,
      {},
      {
        headers: {
          Authorization: token.value
        }
      }
    );
    isLoading.value = false;
    getTodos();
  } catch (error) {
    console.log(error);
  }
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

const incomplete = computed(() => {
  return todos.value.filter((item) => !item.status);
});

const complete = computed(() => {
  return todos.value.filter((item) => item.status);
});

onMounted(() => {
  const authInfo = document.cookie
    .split('; ')
    .find((row) => row.startsWith('authInfo='))
    ?.split('=')[1];
  if (authInfo) {
    nickName.value = JSON.parse(authInfo).user;
    token.value = JSON.parse(authInfo).token;
    getTodos();
  } else {
    router.push('/login');
  }
});
</script>

<template>
  <Loading v-if="isLoading" />
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
        <li><a href="#" @click.prevent="signOut">登出</a></li>
      </ul>
    </nav>
    <div class="conatiner todoListPage vhContainer">
      <div class="todoList_Content">
        <div class="inputBox">
          <input v-model.trim="newTodo" type="text" placeholder="請輸入待辦事項" />
          <a href="#" @click.prevent="addTodo">
            <i class="fa fa-plus"></i>
          </a>
        </div>
        <template v-if="todos.length <= 0">
          <div>目前尚無待辦事項</div>
        </template>
        <div class="todoList_list" v-else>
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
            <!-- 全部 -->
            <TodoList
              v-if="tabActiveIndex === 0"
              :data="todos"
              :delete-todo="deleteTodo"
              :toggle-status="toggleStatus"
            />
            <!-- 待完成 -->
            <TodoList
              v-else-if="incomplete && tabActiveIndex === 1"
              :data="incomplete"
              status="incomplete"
              :delete-todo="deleteTodo"
              :toggle-status="toggleStatus"
            />
            <!-- 已完成 -->
            <TodoList
              v-else
              :data="complete"
              status="complete"
              :delete-todo="deleteTodo"
              :toggle-status="toggleStatus"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped></style>
