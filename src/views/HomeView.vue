<template>
  <UserNewForm
    :errors="errors"
    @createUser="createUser"
  />

  <div class="container home-container">
     <div class="row">
       <div class="col-sm-12">
        <h5 class="card-title top-card-title">脱毛サロン エピア祖師谷 epiR</h5>
        <h6 class="card-title top-card-title">予約サイト</h6>
        <div class="card-body home" id="card-home">
          <img src="@/assets/home-illustration.png" class="card-img-home">
          <p class="card-text" id="text-home">ご利用には登録・ログインが必要です</p>
          <a class="btn btn-primary" href="/login">ログイン</a>
          <a class="btn btn-primary" @click="openNewUserDialog()">登録して予約</a>
          <!-- <p>or</p>
          <a href="/appointments" class="btn btn-primary">Continue as guest</a> -->
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
  import * as bootstrap from 'bootstrap'
  import UserNewForm from './components/UserNewForm.vue'
  export default {
    name: 'HomeView',
    components: {
      UserNewForm,
    },
    data() {
      return {
        errors: null,
        newUser: {},
        newUserDialog: null,
      }
    },
    mounted() {
      this.newUserDialog = new bootstrap.Modal(document.getElementById('new-user-dialog'))
    },
    methods: {
      createUser(user) {
        let loginInfo = {
          email: user.email,
          password: user.password
        }
        axios.post('/users', user)
        .then(()=> {
          this.newUserDialog.hide();
        })
        .then(()=> {
          this.login(loginInfo);
        })
        .catch((error)=> {
          this.errors = error.response;
        })
      },
      login(loginInfo) {
        axios.post('/sessions', loginInfo)
        .then((res)=> {
          axios.defaults.headers.common["Authorization"] = "Bearer " + res.data.jwt;
          localStorage.setItem("jwt", res.data.jwt);
          localStorage.setItem("user_id", res.data.user_id);
          this.$router.push('/appointments');
        })
        .catch((error)=> {
          this.errors = error.response;
        })
      },
      openNewUserDialog() {
        // let password_base = '1234567890abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
        // function genPassword(length = 10)
        // {
        //   let password = '';
        //   for (let i = 0; i < length; i++) {
        //     password += password_base.charAt(Math.floor(Math.random() * password_base.length));
        //   }
        //   return password
        // }
        // let pw = genPassword()
        // this.newUser.password = pw;
        this.newUserDialog.show();
      },
      closeNewUserDialog() {
        this.newUserDialog.hide();
      },
    },
  }
</script>

<style>
.card-img-home {
  max-width: 400px;
}
.top-card-title {
  margin-top: 8px;
}
.btn {
  margin: 5px 8px;
}
#text-home {
  max-width: 400px;
}
.home-container {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
