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
        <div class="column is-three-quarters">
          <h1 class="title" style="color: white;">Microblog - Share Your Thoughts!</h1>
        </div>
        <div class="column">
            <img src="../assets/DW_wordmark.png"/>
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
          <hr class="hr">
          <Post v-for="post in posts" :key="post.id" :post="post"/>
        </div>
        <div class="box column">
          <h3 class="is-size-4 has-text-weight-bold">Users list</h3>
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
            <b-input v-model="message" maxlength="140" type="textarea"/>
          </b-field>
          <b-button type="is-dark" style="background-color: #22243c;" @click="addPost">Submit</b-button>
        </div>
        <hr class="hr">
        <Post v-for="post in posts" :key="post.id" :post="post"/>
      </div>
    </div>
  </div>
</template>

<script>
import Post from '@/components/Post.vue'
import axios from 'axios'
import { mapGetters, mapState } from 'vuex'
import { Flags } from '../utils/flags'

export default {
  name: 'posts',
  components: {
    Post
  },
  data: function () {
    return {
      message: '',
      posts: [],
      users: [],
      errors: [],
      show_sidebar: Flags.sidebar.isEnabled(),
      show_title: Flags.title.isEnabled(),
      show_logo: Flags.logo.isEnabled()
    }
  },
  created () {
    this.getPosts()
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
    getPosts: function () {
      axios.get(`${process.env.VUE_APP_BASE_API_URL}/posts/`)
        .then(response => {
          this.posts = response.data
        })
        .catch(error => {
          this.errors.push(error)
        })
    },
    getUsers: function () {
      axios.get(`${process.env.VUE_APP_BASE_API_URL}/users/`)
        .then(response => {
          this.users = response.data
        })
        .catch(error => {
          this.errors.push(error)
        })
    },
    addPost: function () {
      if (this.message.length > 1 && this.message.length <= 140) {
        axios.post(`${process.env.VUE_APP_BASE_API_URL}/posts/`, {
          user: this.user.url,
          message: this.message
        }, {
          headers: {
            'Authorization': `Bearer ${localStorage.getItem('token')}`,
            'Content-Type': 'application/json'
          }
        })
          .then(() => {
            this.getPosts()
            this.message = ''
          })
          .catch(e => {
            this.errors.push(e)
          })
      }
    }
  }
}
</script>
