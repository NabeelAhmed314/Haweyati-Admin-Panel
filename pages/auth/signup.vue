<template>
  <div
    style="width: 100%;height: 100%;background-color:#fff;border-top-left-radius:40px;padding: 10px"
  >
    <v-container
      style="height: 100%;display: flex;align-items: center ;justify-content: center"
    >
      <v-card v-if="success" style="padding: 20px;width: 400px;">
        <v-container
          style="display: flex;justify-content: center;align-items: center;"
        >
          <img
            src="../../assets/Haweyati_Logo.png"
            alt="logo"
            width="130px"
            height="130px"
          />
        </v-container>
        <v-container style="display: flex;justify-content: center">
          <h3>Successfully registered new Admin</h3>
        </v-container>
      </v-card>
      <v-card v-if="!success" style="padding: 20px;width: 400px;">
        <v-container
          style="display: flex;justify-content: center;align-items: center;"
        >
          <img
            src="../../assets/Haweyati_Logo.png"
            alt="logo"
            width="130px"
            height="130px"
          />
        </v-container>
        <v-container style="display: flex;justify-content: center;padding: 0">
          <v-card-title>Register Admin</v-card-title>
        </v-container>
        <v-progress-linear
          v-if="loading"
          indeterminate
          color="#313F53"
          class="mb-0"
        ></v-progress-linear>
        <v-form v-if="!loading" ref="form">
          <ul v-if="errors.length" style="color: red;margin-bottom: 15px">
            <li v-for="(error, i) of errors" :key="i">
              {{ error }}
            </li>
          </ul>
          <v-container>
            <ImageSelector
              v-model="imageFile"
              :image="signup"
              @input="sendImage = $event"
            />
          </v-container>
          <v-text-field
            v-model="signup.name"
            :value="signup.name"
            color="#313F53"
            :rules="[required]"
            outlined
            style="color: #313F53"
            label="Name"
            dense
          ></v-text-field>
          <v-text-field
            v-model="signup.email"
            :value="signup.email"
            color="#313F53"
            :rules="[required, emailValidator]"
            outlined
            style="color: #313F53"
            label="Email"
            dense
          ></v-text-field>
          <v-text-field
            v-model="signup.contact"
            v-mask="['+### - #######', '+#### - ########']"
            :value="signup.contact"
            :rules="[required, phoneValidator]"
            color="#313F53"
            outlined
            style="color: #313F53"
            label="Phone"
            dense
          ></v-text-field>
          <v-text-field
            v-model="signup.password"
            :value="signup.password"
            :rules="[required, lengthValidator]"
            color="#313F53"
            outlined
            style="color: #313F53"
            type="password"
            label="Password"
            dense
          ></v-text-field>
          <v-text-field
            v-model="signup.confirmPassword"
            :value="signup.confirmPassword"
            color="#313F53"
            :rules="[required, lengthValidator]"
            outlined
            type="password"
            style="color: #313F53"
            label="Confirm Password"
            dense
            @keypress.enter="createUser"
          ></v-text-field>
          <v-btn
            width="100%"
            color="#FF974D"
            style="color:#ffffff"
            large
            elevation="0"
            @click="createUser"
          >
            Sign Up
          </v-btn>
        </v-form>
      </v-card>
    </v-container>
  </div>
</template>

<script>
import {
  required,
  emailValidator,
  lengthValidator,
  phoneValidator
} from '../../common/utils/validators'
import ImageSelector from '../../components/misc/image-selector'

export default {
  components: {
    ImageSelector
  },
  data: () => ({
    loading: false,
    success: false,
    errors: [],
    signup: {
      name: '',
      email: '',
      password: '',
      confirmPassword: '',
      contact: '',
      type: 0,
      scope: 'admin',
      image: ''
    },
    imageFile: null,
    sendImage: null
  }),
  methods: {
    required,
    lengthValidator,
    phoneValidator,
    emailValidator,
    returnBack() {
      this.$router.back()
    },
    async createUser() {
      if (this.$refs.form.validate()) {
        try {
          this.loading = true
          this.errors = []
          if (this.signup.password !== this.signup.confirmPassword) {
            this.loading = false
            this.errors.push('Could not confirm password.')
            return
          }
          const formData = new FormData()

          formData.append('name', this.signup.name)
          formData.append('email', this.signup.email)
          formData.append('password', this.signup.password)
          formData.append(
            'contact',
            this.signup.contact.replace(/[^+0-9]/g, '')
          )
          formData.append('scope', this.signup.scope)
          if (this.sendImage) {
            formData.append('image', this.sendImage)
          }

          formData.forEach((item) => window.console.log(item))
          await this.$axios.post('persons', formData)
          this.loading = false
          this.success = true
        } catch (err) {
          this.loading = false
          if (err.response) {
            this.errors.push(err.response.data.message)
          } else {
            this.errors.push('Unable to process, Try again later')
          }
        }
      }
    }
  }
}
</script>

<style scoped></style>
