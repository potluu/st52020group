<template>
  <v-app-bar app color='#fff'>
    <a href='/'><img id='logo' src='@/assets/images/reddit.png' /></a>

    <v-spacer></v-spacer>
    <GlobalSearch />
    <v-spacer></v-spacer>

    <v-menu offset-y min-width='110' v-if='showHamburgerMenu'>
      <template v-slot:activator='{ on, attrs }'>
        <v-btn
          data-testid='hamburger-menu'
          dark
          icon
          v-bind='attrs'
          v-on='on'
          color='black'
        >
          <v-icon>menu</v-icon>
        </v-btn>
      </template>

      <v-list>
        <v-list-item
          v-for='(item, i) in isAuthenticated ? itemsAuth : itemsNoAuth'
          :key='i'
        >
          <v-list-item-title @click='item.action'>{{ item.title }}</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-menu>

    <div v-if='!showHamburgerMenu'>
      <LogInButton />
      <SignUpButton />
    </div>

    <LogInModal />
    <SignUpModal />
  </v-app-bar>
</template>

<script>
import LogInModal from '@/components/Modals/LogIn.vue'
import SignUpModal from '@/components/Modals/SignUp.vue'
import LogInButton from '@/components/Buttons/LogIn.vue'
import SignUpButton from '@/components/Buttons/SignUp.vue'
import GlobalSearch from '@/components/Core/GlobalSearch.vue'
import axios from 'axios'

export default {
  components: {
    LogInModal,
    SignUpModal,
    LogInButton,
    SignUpButton,
    GlobalSearch
  },
  data: function () {
    return {
      itemsAuth: [
        {
          title: 'User Settings',
          action: () => this.$router.push('/settings')
        },
        {
          title: 'Log Out',
          action: () => this.logout()
        }
      ],
      itemsNoAuth: [
        {
          title: 'Log In',
          action: () => this.$store.commit('setModal', 'log-in')
        },
        {
          title: 'Sign Up',
          action: () => this.$store.commit('setModal', 'sign-up')
        }
      ]
    }
  },
  methods: {
    logout () {
      axios.get('/api/users/logout')
        .then(() => {
          window.location.href = '/'
        })
    }
  },
  computed: {
    isAuthenticated () {
      return this.$store.state.isAuthenticated
    },
    showHamburgerMenu () {
      return this.isAuthenticated || this.$vuetify.breakpoint.xsOnly
    }
  }
}
</script>

<style scoped lang="scss">
  @import '~vuetify/src/styles/styles.sass';
  a {
    display: flex;
  }
  #logo {
    width: 40px;
    cursor: pointer;
  }
  .log-in-button {
    margin-right: 20px;
    color: black !important;
  }
  .v-list {
    padding: 0;
  }
  .v-list-item {
    cursor: pointer;
    &:hover {
      background: rgb(246, 246, 246);
    }
  }

  @media #{map-get($display-breakpoints, 'sm-and-up')} {
    .v-list-item__title {
      font-size: 14px !important;
    }
  }

  @media #{map-get($display-breakpoints, 'xs-only')} {
    #logo {
      width: 35px;
    }
  }
</style>
