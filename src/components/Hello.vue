<template>
  <div class="hello">
    <button @click="login('user1@email.com', 'password')">Login user 1</button>
    <button @click="login('user2@email.com', 'password')">Login user 2</button>
    <button @click="loadCurrentUser">Load user</button>
    <button @click="logout">logout</button>
    <p>
      {{user}}
    </p>
  </div>
</template>

<script>
import gql from 'graphql-tag'

const SignInQuery = gql `
  mutation signinUser($email: String!, $password: String!) {
    signinUser(
      email: {
        email: $email,
        password: $password
      }
    ) {
      token
      user {
        id
      }
    }
  }
`

const QueryUser = gql `
query {
  user {
    id,
    firstName,
    lastName,
    email
  }
}`

export default {
  name: 'hello',
  data () {
    return {
      user: {}
    }
  },
  methods: {
    login (email, password) {
      console.log(email)
      this.$apollo.mutate({
        mutation: SignInQuery,
        variables: {
          email,
          password
        }
      }).then((response) => {
        console.log(response.data.signinUser.token)
        localStorage.setItem('USER_TOKEN', response.data.signinUser.token)
        return this.getCurrentUser()
      }).then((response) => {
        this.user = response.data
      }).catch((error) => {
        console.log('error coming form here')
        console.log(error)
      })
    },
    logout () {
      console.log('logout')
      this.user = {}
      localStorage.setItem('USER_TOKEN', null)
    },
    loadCurrentUser () {
      this.getCurrentUser().then((response) => {
        this.user = response.data
      })
    },
    getCurrentUser () {
      return this.$apollo.query({
        query: QueryUser
      })
    }
  }
}
</script>
