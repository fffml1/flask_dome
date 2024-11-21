<template>
  <div id="app">
    <h1>用户管理系统</h1>

    <!-- 注册 -->
    <div class="form">
      <h2>注册</h2>
      <input v-model="registerForm.userName" placeholder="用户名" />
      <input v-model="registerForm.password" type="password" placeholder="密码" />
      <button @click="register">注册</button>
    </div>

    <!-- 登录 -->
    <div class="form">
      <h2>登录</h2>
      <input v-model="loginForm.userName" placeholder="用户名" />
      <input v-model="loginForm.password" type="password" placeholder="密码" />
      <button @click="login">登录</button>
    </div>

    <!-- 查询用户信息 -->
    <div v-if="isLoggedIn" class="form">
      <h2>查询用户信息</h2>
      <button @click="getUserInfo">查询</button>
      <div v-if="userInfo">
        <p>用户名：{{ userInfo.userName }}</p>
        <p>用户密码：{{ userInfo.password }}</p>
        <p>用户类型：{{ userInfo.userType }}</p>
      </div>
    </div>
  </div>
  <div style="display: flex; justify-content: flex-end;">

  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      registerForm: {
        userName: "",
        password: "",
      },
      loginForm: {
        userName: "",
        password: "",
      },
      token: "", // 保存 token
      userInfo: null, // 用户信息
    };
  },
  computed: {
    isLoggedIn() {
      return this.token !== ""; // 判断是否已登录
    },
  },
  methods: {
    // 注册
    async register() {
      try {
        const response = await axios.post("/api/user/register", {
          userName: this.registerForm.userName,
          password: this.registerForm.password,
        });
        alert(response.data.msg);
      } catch (error) {
        alert(error.response?.data?.msg || "注册失败");
      }
    },

    // 登录
    async login() {
      try {
        const response = await axios.post("/api/user/login", {
          userName: this.loginForm.userName,
          password: this.loginForm.password,
        });
        if (response.data.code === 0) {
          this.token = response.data.data.token; // 保存 token
          alert("登录成功");
        } else {
          alert(response.data.msg);
        }
      } catch (error) {
        alert(error.response?.data?.msg || "登录失败");
      }
    },

    // 查询用户信息
    async getUserInfo() {
      try {
        const response = await axios.get("/api/user/info", {
          headers: {
            token: this.token, // 携带 token
          },
        });
        if (response.data.code === 0) {
          this.userInfo = response.data.data; // 保存用户信息
        } else {
          alert(response.data.msg);
        }
      } catch (error) {
        alert(error.response?.data?.msg || "查询用户信息失败");
      }
    },
    // 获取用户列表
    async fetchUsers() {
      try {
        const response = await axios.get("/api/user/all");
        if (response.data.code === 0) {
          this.users = response.data.data; // 更新用户列表
        } else {
          alert(response.data.msg);
        }
      } catch (error) {
        alert(error.response?.data?.msg || "获取用户列表失败");
      }
    },
  },
};
</script>

<style>
#app {
  text-align: center;
}

.form {
  margin: 20px auto;
  padding: 10px;
  width: 300px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

input {
  display: block;
  margin: 10px auto;
  padding: 5px;
  width: 90%;
}

button {
  padding: 5px 10px;
  cursor: pointer;
}
</style>
