<template>

  <nav class="navbar navbar-light" style="background-color: #f5f6fe;">
    <div class="col-12 user-btn-container">
      <button
        class="btn btn-sm btn-outline-success"
        v-if="history"
        @click="showHistory(false)"
      >
        ユーザー詳細
      </button>
      <button
        class="btn btn-sm btn-outline-success"
        v-if="!history"
        @click="showHistory(true)"
      >
        予約履歴
      </button>
      <div class="control-navbar-item">
        <button class="btn btn-sm btn-outline-secondary" @click="$router.push('/admin/users')">全ユーザー</button>
      </div>
    </div>
  </nav>

  <div class="container">
    <UserDetailForm
      :userProps="user"
      @updateUser="updateUser"
      v-if="!history"
    />

    <div class="row" v-if="history">
      <div class="col-12">
        <h4>予約履歴</h4>
        <small>予約回数: {{ events.length }} | </small>
        <small>総売上: ¥{{ totalSpent }} | </small>
        <small>最終予約日: {{ lastVisit }}</small>
      </div>
      <hr>
      <div
        class="col-sm-2 history-event-container card"
        v-for="event in events"
        :key="event.id"
      >
        <div class="card-body">
          <small class="card-subtitle">{{ event.status }}</small>
          <h6 class="card-title">{{ event.date }}</h6>
          <ul class="list-group list-group-flush">
            <li
              class="list-group-item"
              v-for="menu in event.menus"
              :key="menu.id"
            >
              {{ menu.title }}
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>  
</template>

<script>
import axios from 'axios'
import UserDetailForm from '../components/UserDetailForm.vue'
  export default {
    components: {
      UserDetailForm,
    },
    data() {
      return {
        user: {
          events: [],
        },
        genders: [
          "Male",
          "Female",
          "N/A",
          "Rather not to say"
        ],
        states: [
          "AL", "AK", "AZ", "AR", "CA",
          "CO", "CT", "DE", "FL", "GA",
          "HI", "ID", "IL", "IN", "IA",
          "KS", "KY", "LA", "ME", "MD",
          "MA", "MI", "MN", "MS", "MO",
          "MT", "NE", "NV", "NH", "NJ",
          "NM", "NY", "NC", "ND", "OH",
          "OK", "OR", "PA", "RI", "SC",
          "SD", "TN", "TX", "UT", "VT",
          "VA", "WA", "WV", "WI", "WY"
        ],
        message: null,
        history: false
      }
    },
    created() {
      this.showUser();
    },
    computed: {
      events() {
        let events = this.user.events;
        let sortedEvents = events.sort((a, b)=> {
          let idA = a.date_and_start;
          let idB = b.date_and_start;
          if (idA < idB) {
            return -1;
          }
          if (idA > idB) {
            return 1;
          }
          return 0
        });
        return sortedEvents;
      },
      totalSpent() {
        if (this.events.length === 0) {
          return 0
        }
        let booked = this.events.filter((event)=> event.status === "booked")
        return booked.map((event)=> event.total_spent).reduce((sum, price)=> sum + price, 0)
      },
      lastVisit() {
        if (this.events.length === 0) {
          return "N/A"
        }
        let events = this.user.events;
        let sortedEvents = events.sort((a, b)=> {
          let idA = a.date_and_start;
          let idB = b.date_and_start;
          if (idA < idB) {
            return -1;
          }
          if (idA > idB) {
            return 1;
          }
          return 0
        });
        return sortedEvents[sortedEvents.length -1].date;
      },
    },
    methods: {
      showUser() {
        let id = this.$route.params.id
        axios.get(`/users/${id}.json`)
        .then((res)=> {
          this.user = res.data;
        })
      },
      updateUser() {
        let id = this.user.id;
        let user = {}
        user = {
          first_name: this.user.first_name,
          last_name: this.user.last_name,
          gender: this.user.gender,
          email: this.user.email,
          phone: this.user.phone,
          birthday: this.user.birthday,
          address: this.user.address,
          state: this.user.state,
          city: this.user.city,
          zip: this.user.zip,
          status: this.user.status,
          note: this.user.note
        }
        axios.patch(`/users/${id}.json`, user)
        .then((res)=> {
          this.user = res.data
        })
        this.message = "User updated";
        setTimeout(()=> {this.message = null}, 3000);
      },
      showHistory(boolean) {
        this.history = boolean;
      },
    },
  }
</script>

<style scoped>
  .row {
    text-align: left;
  }
  .btn-container {
    margin-top: 15px;
  }
  .user-btn-container {
    text-align: left;
    overflow: hidden;
  }
  .control-navbar-item {
    float: right;
  }
  .history-event-container {
    text-align: left;
  }
  .notification {
    color: red;
  }
</style>