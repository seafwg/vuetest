<template>
  <div>
    <MyFrom :model="model" :rules="rules" ref="myFrom">
      <MyInputItem label="用户名" prop="username">
        <MyInput v-model="model.user"></MyInput>
      </MyInputItem>
      <MyInputItem label="用户密码" prop="password">
        <MyInput v-model="model.password"></MyInput>
      </MyInputItem>
      <MyInputItem>
        <button @click="handleLogin">登录</button>
      </MyInputItem>
    </MyFrom>

    {{model}}
  </div>
</template>

<script>
import MyInput from "./myInput.vue";
import MyInputItem from "./myInputItem";
import MyFrom from "./myFrom";

export default {
  components: {
    MyInput,
    MyInputItem,
    MyFrom,
  },
  data() {
    return {
      model: {
        user: '',
        password: ''
      },
      rules:{
        username: [{ required: true, message: "用户名必填" }],
        password: [{ required: true, message: "密码必填" }]
      }
    }
  },
  methods: {
    handleLogin() {
      this.$refs.myFrom.validate((isValidate) => {
        if(isValidate) {
          alert('可以登录');
        }else{
          alert('请输入正确的信息');
        }
      })
    }
  },
};
</script>

<style>
</style>