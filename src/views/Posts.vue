<template>
  <div class="container">
    <hr class="hr is-invisible">
    <div class="box" style="background-color: #22243c;">
      <div class="columns" v-if="show_logo">
        <div class="column is-three-quarters">
          <h1 class="title" style="color: white;">Microblog - Share Your Thoughts!</h1>
        </div>
        <div class="column">
            <img src="../assets/DW_wordmark.png"/>
        </div>
      </div>
      <div class="columns" v-else>
        <div class="column">
          <h1 class="title" style="color: white;">Microblog - Share Your Thoughts!</h1>
        </div>
      </div>
      <div class="columns" v-if="show_sidebar">
        <div class="box column is-three-quarters">
          <div class="box">
            <b-field label="What's going on today?"
                     class="is-marginless"
            >
              <b-input v-model="message" maxlength="140" type="textarea"/>
            </b-field>
            <b-button type="is-dark" @click="addPost">Submit</b-button>
          </div>
          <ul>
            <li><span style="font-weight: bold; color: green;">Elena Benoit 9:37 am:</span> Really enjoying today's keynote!</li>
            <li><span style="font-weight: bold; color: green;">Brian Dawson 9:37 am:</span> First demo was awesome, looking forward to more product integrations.</li>
            <li><span style="font-weight: bold; color: green;">Kathy Lam 9:39 am:</span> Feature flags are the most underrated technology in software development today. Fight me on this.</li>
          </ul>
        </div>
        <div class="box column">
          <h3 class="is-size-4 has-text-weight-bold">Users</h3>
          <ul>
            <li>Shawn Ahmed</li>
            <li style="font-weight: bold; color: green;">Emmanuel Bamishaye</li>
            <li style="font-weight: bold; color: green;">Elena Benoit</li>
            <li>Anand Chauhan</li>
            <li style="font-weight: bold; color: green;">Brian Dawson</li>
            <li>Logan Donley</li>
            <li style="font-weight: bold; color: green;">Kathy Lam</li>
            <li>Doug Tidwell</li>
          </ul>
        </div>
      </div>

      <div class="box" v-else>
        <div class="box">
          <b-field label="What's going on today?"
                   class="is-marginless"
          >
            <b-input v-model="message" maxlength="140" type="textarea" text="Yo!"/>
          </b-field>
          <b-button type="is-dark" style="background-color: #22243c;">Submit</b-button>
        </div>
        <hr class="hr">
        <ul>
          <li><span style="font-weight: bold; color: green;">Elena Benoit 9:37 am:</span> Really enjoying today's keynote!</li>
          <li><span style="font-weight: bold; color: green;">Brian Dawson 9:37 am:</span> First demo was awesome, looking forward to more product integrations.</li>
          <li><span style="font-weight: bold; color: green;">Kathy Lam 9:39 am:</span> Feature flags are the most underrated technology in software development today. Fight me on this.</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { mapGetters, mapState } from 'vuex'
import { Flags } from '../utils/flags'

export default {
  data: function () {
    return {
      message: '',
      users: [],
      errors: [],
      show_sidebar: Flags.sidebar.isEnabled(),
      show_title: Flags.title.isEnabled(),
      show_logo: Flags.logo.isEnabled()
    }
  },
  created () {
    this.getUsers()
  },
  computed: {
    ...mapGetters([
      'isLoggedIn'
    ]),
    ...mapState([
      'user'
    ])
  },
  methods: {
    getUsers: function () {
      axios.get(`${process.env.VUE_APP_BASE_API_URL}/users/`)
        .then(response => {
          this.users = response.data
        })
        .catch(error => {
          this.errors.push(error)
        })
    }
  }
}
</script>
