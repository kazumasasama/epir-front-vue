<template>
  <div class="container">
    <h4 class="page-title">ユーザー登録</h4>
      <div class="col-12">
        <form v-on:submit.prevent="createUser()" class="col-12">
          <div class="row">
            <div class="col-sm-6">
              <p><strong>必須項目*</strong></p>
              <small>姓*</small>
              <input type="text" v-model="user.first_name" class="form-control">
              <small>名*</small>
              <input type="text" v-model="user.last_name" class="form-control">
              <small>メールアドレス*</small>
              <input type="email" v-model="user.email" class="form-control">
              <small>パスワード*</small>
              <input type="password" v-model="user.password" class="form-control">
              <small>パスワード確認*</small>
              <input type="password" v-model="user.passwordConfirm" class="form-control">
            </div>
            <div class="col-sm-6">
              <p><strong>任意項目</strong></p>
              <small>電話番号</small>
              <input type="text" v-model="user.phone" class="form-control">
              <small>郵便番号</small>
              <input type="text" v-model="user.zip" class="form-control">
              <small>都道府県</small>
              <select
                v-model="user.state"
                class="form-select"
                autocomplete="address-level1"
              >
                <option
                  v-for="state in states"
                  :key="state"
                  :value="state"
                >
                  {{ state }}
                </option>
              </select>
              <small>市区町村</small>
              <input type="text" v-model="user.city" class="form-control">
              <small>以下の住所</small>
              <input type="text" v-model="user.address" class="form-control">
              <small>性別</small>
              <select v-model="user.gender" class="form-select">
                <option
                  v-for="gender in genders"
                  :key="gender"
                  :value="gender"
                >
                  {{ gender }}
                </option>
              </select>
              <small>生年月日</small>
              <input
                type="date"
                v-model="user.birthday"
                class="form-control"
                placeholder="12/31/2022"
              >
            </div>
            <div class="btn-container col-sm-6">
              <button
                type="button"
                class="btn btn-secondary"
                @click="cancelSignup()"
              >
                ホームに戻る
              </button>
              <button type="submit" class="btn btn-primary">登録する</button>
            </div>
          </div>
        </form>
      </div>
  </div>
</template>

<script>
import axios from "axios"
  export default {
    data() {
      return {
        user: {},
        genders: [
          "男性",
          "女性",
          "該当なし",
          "回答しない"
        ],
        states: [
          "北海道", "青森県", "岩手県", "宮城県", "秋田県",
          "山形県", "福島県", "茨城県", "栃木県", "群馬県",
          "埼玉県", "千葉県", "東京都", "神奈川県", "新潟県",
          "富山県", "石川県", "福井県", "山梨県", "長野県",
          "岐阜県", "静岡県", "愛知県", "三重県", "滋賀県",
          "京都府", "大阪府", "兵庫県", "奈良県", "和歌山県",
          "鳥取県", "島根県", "岡山県", "広島県", "山口県",
          "徳島県", "香川県", "愛媛県", "高知県", "福岡県",
          "佐賀県", "長崎県", "熊本県", "大分県", "宮崎県",
          "鹿児島県", "沖縄県"
        ],
      }
    },
    methods: {
      createUser() {
        if (this.user.password === this.user.passwordConfirm) {
          axios.post('/users', this.user)
          .then((res)=> {
            localStorage.setItem("user_id", res.data.user_id);
            let user = {
              email: res.data.email,
              password: this.user.password
            }
            axios.post('/sessions', user)
            .then((res)=> {
              axios.defaults.headers.common["Authorization"] = "Bearer " + res.data.jwt;
              localStorage.setItem("jwt", res.data.jwt);
              localStorage.setItem("user_id", res.data.user_id);
            })
            this.user = {};
            this.$router.push('/appointments');
          })
          .catch((error)=> {
            console.log(error);
          })
        }
      },
      cancelSignup() {
        this.user = {};
        this.$router.push('/');
      },
    }
  }
</script>

<style scoped>
  .col-sm-6 {
    text-align: left;
  }
  .page-title {
    margin-bottom: 30px;
  }
</style>