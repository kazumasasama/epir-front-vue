<template>
  <div class="container">
    <div>
      <h4>ログイン</h4>
    </div>
    <div class="row">
      <div class="col-sm-12 login-form-container">
        <div class="login-form-container">
          <form v-on:submit.prevent="login()">
            <small>メールアドレス</small>
            <input type="text" v-model="user.email" class="form-control">
            <small>パスワード</small>
            <input type="password" v-model="user.password" class="form-control">
            <div class="btn-container">
              <button type="button" class="btn btn-secondary" @click="toHome()">ホームに戻る</button>
              <button type="submit" class="btn btn-primary">ログイン</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
  export default {
    data() {
      return {
        user: {
          email: "",
          password: "",
        },
      }
    },
    methods: {
      login() {
        axios.post('/sessions', this.user)
        .then((res)=> {
          axios.defaults.headers.common["Authorization"] = "Bearer " + res.data.jwt;
          localStorage.setItem("jwt", res.data.jwt);
          localStorage.setItem("user_id", res.data.user_id);
          if (res.data.admin == true) {
            this.$router.push('/admin/calendar');
          } else {
            this.$router.push('/appointments');
          }
        })
        .catch((error)=> {
          console.log(error.response);
        })
      },
      toHome() {
        this.user = {};
        this.$router.push('/');
      },
    }
  }
</script>

<style scoped>
  .row {
    text-align: left;
  }
  .login-form-container {
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .container {
    max-width: 300px;
  }
  .btn-container {
    margin-top: 20px;
  }
</style>