<template>
  <div class="ui stackable grid container">
    <UserSettingsMenu/>

    <div class="eight wide column">
      <div class="ui segment">
        <h2 class="ui dividing header">Change Password</h2>

        <Notification
          :message="notification.message"
          :type="notification.type"
          v-if="notification.message"
        />

        <form class="ui form" @submit.prevent="changePassword">
          <div class="field">
            <label>Current Password</label>
            <input type="password" name="password" v-model="password">
            <span class="is-danger"></span>
          </div>

          <div class="field">
            <label>New Password</label>
            <input type="password" name="newPassword" v-model="newPassword">
           <span class="is-danger"></span>
          </div>

          <div class="field">
            <label>New Password</label>
            <input type="password" name="confirmPassword" v-model="confirmPassword">
            <span class="is-danger"></span>
          </div>

          <button type="submit" class="ui button primary">Change password</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import Notification from '@/components/Notification'
import UserSettingsMenu from '@/components/User/Settings/UserSettingsMenu'

export default {
  name: 'UserProfileEditForm',
  components: {
    Notification,
    UserSettingsMenu
  },
  data () {
    return {
      password: '',
      newPassword: '',
      confirmPassword: '',
      notification: {
        message: '',
        type: ''
      }
    }
  },
  computed: {
    isFormValid () {
      return true
    }
  },
  beforeRouteEnter (to, from, next) {
    const token = localStorage.getItem('tweetr-token')

    return token ? next() : next('/login')
  },
  methods: {
    changePassword () {
      const token = localStorage.getItem('tweetr-token')

      axios
        .put(
          '/account/change_password',
          {
            password: this.password,
            newPassword: this.newPassword
          },
          {
            headers: {
              Authorization: `Bearer ${token}`
            }
          }
        )
        .then(response => {
          // clear form inputs
          this.password = this.newPassword = this.confirmPassword = ''

          // display success notification
          this.notification = Object.assign({}, this.notification, {
            message: response.data.message,
            type: 'success'
          })
        })
        .catch(error => {
          // clear form inputs
          this.password = this.newPassword = this.confirmPassword = ''

          // display error notification
          this.notification = Object.assign({}, this.notification, {
            message: error.response.data.message,
            type: 'negative'
          })
        })
    }
  }
}
</script>
