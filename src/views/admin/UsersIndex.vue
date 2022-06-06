<template>
  <UserNewForm
    @createUser="createUser"
  />

  <nav class="navbar navbar-light" style="background-color: #f5f6fe;">
    <div class="col-12 users-btn-container">
      <button class="btn btn-outline-success btn-sm" @click="openNewUserDialog()">新規ユーザー</button>
      <div class="control-navbar-item">
        <small>並び替え</small>
        <button class="btn btn-outline-primary btn-sm" @click="sortById()">ユーザーID</button>
        <button class="btn btn-outline-primary btn-sm" @click="sortByFirstName()">姓</button>
        <button class="btn btn-outline-primary btn-sm" @click="sortByLastName()">名</button>
      </div>
    </div>
  </nav>

  <div>
    <p v-if="errors">{{ errors }}</p>
  </div>

  <div class="container">

    <div class="row">
      <small>クリックして詳細表示</small>
      <div class="col-md-4" v-for="user in users" :key="user.id">
        <div class="list-group" id="list-tab" @click="this.$router.push(`/admin/users/${user.id}`)">
          <a
            class="list-group-item list-group-item-action"
            @click="showUser(user)"
            role="tab"
          >
            <div class="d-flex w-100 justify-content-between">
              <small>No.{{ user.id }}</small>
              <small>{{ user.status }}</small>
            </div>
            <h6 class="mb-1">{{ user.full_name }}</h6>
            <small class="mb-1">{{ user.email }}</small>
          </a>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
  import axios from 'axios'
  import * as bootstrap from 'bootstrap'
  import UserNewForm from '../components/UserNewForm.vue'
  export default {
    components: {
      UserNewForm,
    },
    data() {
      return {
        errors: null,
        users: [],
        user: {},
        sort: "id",
        newUser: {},
        newUserDialog: null,
      }
    },
    created() {
      this.indexUsers();
    },
    mounted() {
      this.newUserDialog = new bootstrap.Modal(document.getElementById('new-user-dialog'))
    },
    methods: {
      indexUsers() {
        axios.get('/users.json')
        .then((res)=> {
          let users = res.data.sort((a, b)=> {
            let idA = a.id;
            let idB = b.id;
            if (idA < idB) {
              return -1;
            }
            if (idA > idB) {
              return 1;
            }
          });
          this.users = users;
        })
      },
      showUser(user) {
        this.user = user;
      },
      sortById() {
        let users = this.users.sort((a, b)=> {
            let idA = a.id;
            let idB = b.id;
            if (idA < idB) {
              return -1;
            }
            if (idA > idB) {
              return 1;
            }
          });
          this.users = users;
      },
      sortByFirstName() {
        let sorted = [];
        let unsorted = this.users
        sorted = unsorted.sort((a, b)=> {
          let nameA = a.first_name.toUpperCase();
          let nameB = b.first_name.toUpperCase();
          if (nameA < nameB) {
            return -1;
          }
          if (nameA > nameB) {
            return 1;
          }
          // names must be equal
          return 0;
        });
        this.sortedByFirstName = sorted;
      },
      sortByLastName() {
        let sorted = [];
        let unsorted = this.users
        sorted = unsorted.sort((a, b)=> {
          let nameA = a.last_name.toUpperCase();
          let nameB = b.last_name.toUpperCase();
          if (nameA < nameB) {
            return -1;
          }
          if (nameA > nameB) {
            return 1;
          }
          // names must be equal
          return 0;
        });
        this.sortedByFirstName = sorted;
      },
      createUser(user) {
        let password_base = '1234567890abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
        function genPassword(length = 10)
        {
          let password = '';
          for (let i = 0; i < length; i++) {
            password += password_base.charAt(Math.floor(Math.random() * password_base.length));
          }
          return password
        }
        let pw = genPassword()
        user.password = pw;
        axios.post('/users', user)
        .then((res)=> {
          this.users.push(res.data);
        })
        .catch((errors)=> {
          this.errors = errors
        })
        .then(()=> {
          this.users = this.users.sort((a, b)=> {
            let idA = a.id;
            let idB = b.id;
            if (idA < idB) {
              return -1;
            }
            if (idA > idB) {
              return 1;
            }
          });
        })
        .then(()=> {
          this.closeNewUserDialog()
        })
      },
      openNewUserDialog() {
        this.newUserDialog.show();
      },
      closeNewUserDialog() {
        this.newUserDialog.hide();
      }
    },
  }
</script>

<style scoped>
  #list-home {
    text-align: left;
  }
  .list-group-item {
    text-align: left;
  }
  .users-btn-container {
    text-align: left;
    overflow: hidden;
  }
  .control-navbar-item {
    float: right;
  }
  .smaller-text {
    font-size: x-small;
  }
  .modal-footer {
    margin-top: 10px;
  }
</style>