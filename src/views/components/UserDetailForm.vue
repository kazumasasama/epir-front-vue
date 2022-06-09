<template>
  <h5 class="user-show-title">ユーザー詳細</h5>
  <form>
    <div class="row">
      <p>{{ errors }}</p>
      <div class="col-sm-6">
        <p><strong>必須項目*</strong></p>
        <small>姓*</small>
        <input
          type="text"
          v-model="user.first_name"
          class="form-control"
          autocomplete="given-name"
        >
        <small>名*</small>
        <input
          type="text"
          v-model="user.last_name"
          class="form-control"
          autocomplete="family-name"
        >
        <small>メールアドレス*</small>
        <input
          type="text"
          v-model="user.email"
          class="form-control"
          autocomplete="email"
        >
        <small>電話番号*</small>
        <input
          type="text"
          v-model="user.phone"
          class="form-control"
          autocomplete="tel-national"
        >
        <small>性別*</small>
        <select
          v-model="user.gender"
          class="form-select"
          autocomplete="sex"
        >
          <option
            v-for="gender in genders"
            :key="gender"
            :value="gender"
          >
            {{ gender }}
          </option>
        </select>
        <div v-if="passwordAppearance">
          <small>パスワード*</small>
          <input
            type="password"
            v-model="user.password"
            class="form-control pwInput"
            autocomplete="new-password"
          >
          <small>パスワード確認*</small>
          <input
            type="password"
            v-model="user.passwordConfirm"
            class="form-control pwInput"
            autocomplete="new-password"
          >
          <input
            id="showPasswordCheckBox"
            type="checkbox"
            class="form-check-input"
            @click="showPassword()"
          />
          <label class="form-check-label" for="showPasswordCheckBox">
            <small>&nbsp;表示する</small>
          </label>
        </div>
      </div>
      <div class="col-sm-6">
        <p><strong>任意項目</strong></p>
        <small>LINE ID</small>
        <input
          type="text"
          v-model="user.line_id"
          class="form-control"
          autocomplete="off"
        >
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
        <input
          type="text"
          v-model="user.city"
          class="form-control"
          autocomplete="address-level2"
        >
        <small>以降の住所</small>
        <input
          autocomplete="street-address"
          type="text"
          v-model="user.address"
          class="form-control"
        >
        <small>郵便番号</small>
        <input
          type="text"
          v-model="user.zip"
          class="form-control"
          autocomplete="postal-code"
        >
        <small>ご要望など</small><br>
        <small class="smaller-text">(お客様画面に表示されます)</small>
        <textarea
          v-model="user.note"
          col-sm-6s="30"
          rows="3"
          class="user-note form-control"
        >
        </textarea>
        <small>生年月日</small>
        <input
          type="date"
          v-model="user.birthday"
          class="form-control"
          autocomplete="bday"
        >
        <small>ステータス</small>
        <select
          v-model="user.status"
          class="form-select"
          autocomplete="address-level1"
        >
          <option
            v-for="status in statuses"
            :key="status"
            :value="status"
          >
            {{ status }}
          </option>
        </select>
      </div>
      <div class="modal-footer btn-container col-sm-12">
        <!-- <button class="btn btn-primary">Update</button> -->
        <button class="btn btn-secondary btn-sm" data-bs-dismiss="modal">閉じる</button>
        <button class="btn btn-warning btn-sm" @click="this.user = {}">クリア</button>
        <button class="btn btn-primary btn-sm" @click="handleUpdateUser()">更新</button>
      </div>
    </div>
  </form>
</template>

<script>
  export default {
    emits: ["updateUser"],
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
          "鹿児島県", "沖縄県", "海外"
        ],
        statuses: [
          "VIP",
          "Friend",
          "!!!",
          "xxx",
        ],
        passwordAppearance: true,
      }
    },
    props: {
      errors: null,
      userProps: {},
    },
    watch: {
      userProps() {
        this.user = this.userProps;
      },
      // $route(to, from) {
      //   from
      //   if (to.path === '/admin/users/:id') {
      //     this.passwordAppearance = false;
      //   }
      // }
    },
    created() {
      this.user = this.userProps;
    },
    computed: {
      showPasswordInputs() {
        if (this.$route.path !== '/admin/users/:id') {
          return true
        } else {
          return false
        }
      },
    },
    methods: {
      handleUpdateUser() {
        this.$emit('updateUser', this.user);
      },
      showPassword() {
        let pwInput = document.querySelectorAll('.pwInput');
        pwInput.forEach((el)=> {
          if (el.type === "password") {
            el.type = "text";
          } else {
            el.type = "password";
          }
        })
      },
    },
  }
</script>

<style scoped>
.btn-container {
  margin-top: 20px;
}
.col-sm-6 {
  text-align: left;
  padding: 15px;
}
.col-sm-4 {
  text-align: left;
  padding: 15px;
}
.user-show-title {
  text-align: left;
}
</style>