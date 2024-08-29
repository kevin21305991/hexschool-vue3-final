<script setup>
import { ref, computed } from 'vue';
import router from '@/router';
import axios from 'axios';

const apiBaseUrl = 'https://todolist-api.hexschool.io';
const registerForm = ref({
  email: '',
  nickname: '',
  password: '',
  passwordCheck: ''
});
const errorMsgArr = ref([]);

const submitHandler = () => {
  if (registerForm.value.password === registerForm.value.passwordCheck) {
    register();
  }
};

const register = async () => {
  for (const field in registerForm.value) {
    if (registerForm.value[field] === '') {
      alert(`${field} 不可為空`);
      return;
    }
  }
  try {
    const response = await axios.post(`${apiBaseUrl}/users/sign_up`, {
      email: registerForm.value.email,
      nickname: registerForm.value.nickname,
      password: registerForm.value.password
    });
    if (response.data.status) {
      //導到登入頁
      router.push('/login');
    }
  } catch (error) {
    errorMsgArr.value = error.response.data.message;
  }
};

const errorMsg = computed(() => {
  if (typeof errorMsgArr.value === 'object') {
    const emailError = errorMsgArr.value.filter((item) => item.includes('email'));
    const nicknameError = errorMsgArr.value.filter((item) => item.includes('nickname'));
    const passwordError = errorMsgArr.value.filter((item) => item.includes('password'));
    return {
      email: emailError.join(', '),
      nickname: nicknameError.join(', '),
      password: passwordError.join(', ')
    };
  } else {
    return {
      email: errorMsgArr.value
    };
  }
});
</script>

<template>
  <!-- sign up -->
  <div id="signUpPage" class="bg-yellow">
    <div class="conatiner signUpPage vhContainer">
      <div class="side">
        <a href="#"
          ><img
            class="logoImg"
            src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/logo.png"
            alt=""
        /></a>
        <img
          class="d-m-n"
          src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/img.png"
          alt="workImg"
        />
      </div>
      <div>
        <form class="formControls">
          <h2 class="formControls_txt">註冊帳號</h2>
          <label class="formControls_label" for="email">Email</label>
          <input
            v-model="registerForm.email"
            class="formControls_input"
            type="text"
            id="email"
            name="email"
            placeholder="請輸入 email"
            required
          />
          <span v-if="errorMsg.email">{{ errorMsg.email }}</span>
          <label class="formControls_label" for="name">您的暱稱</label>
          <input
            v-model="registerForm.nickname"
            class="formControls_input"
            type="text"
            name="name"
            id="name"
            placeholder="請輸入您的暱稱"
          />
          <span v-if="errorMsg.nickname">{{ errorMsg.nickname }}</span>
          <label class="formControls_label" for="pwd">密碼</label>
          <input
            v-model="registerForm.password"
            class="formControls_input"
            type="password"
            name="pwd"
            id="pwd"
            placeholder="請輸入密碼"
            required
          />
          <span v-if="errorMsg.password">{{ errorMsg.password }}</span>
          <label class="formControls_label" for="pwd-check">再次輸入密碼</label>
          <input
            v-model="registerForm.passwordCheck"
            class="formControls_input"
            type="password"
            name="pwd"
            id="pwd-check"
            placeholder="請再次輸入密碼"
            required
          />
          <span v-if="errorMsg.password">{{ errorMsg.password }}</span>
          <button type="button" class="formControls_btnSubmit" @click="submitHandler">
            註冊帳號
          </button>
          <RouterLink to="/" class="formControls_btnLink">登入</RouterLink>
        </form>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped></style>
