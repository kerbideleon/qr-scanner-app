<template>
  <div class="signin-container">
    <v-card
      max-width="420"
      class="pa-6 rounded-xl elevation-10 signin-card"
      color="#1c1c1e"
    >
      <v-card-text>
        <!-- Logo / Header -->
        <div class="text-center mb-6">
          <v-icon size="36" color="deep-purple accent-2">mdi-shield-lock-outline</v-icon>
          <h2 class="text-h5 font-weight-bold mt-2">Sign in to Your Portal</h2>
          <p class="text-caption text--secondary mt-1">
            Access your QR tools and admin dashboard
          </p>
        </div>

        <!-- Login Form -->
        <v-form>
          <v-text-field
            v-model="name"
            label="Username"
            outlined
            dense
            clearable
            rounded
            prepend-inner-icon="mdi-account"
            class="mb-4"
            color="indigo accent-2"
          />

          <v-text-field
            v-model="email"
            label="Password"
            type="password"
            outlined
            dense
            clearable
            rounded
            prepend-inner-icon="mdi-lock"
            class="mb-4"
            color="indigo accent-2"
          />

          <v-btn
            color="indigo accent-3"
            dark
            block
            large
            rounded
            class="mb-4 font-weight-bold"
          >
            Login
          </v-btn>

          <!-- Divider -->
          <div class="divider-text my-4">
            <span>OR</span>
          </div>

          <!-- Google Login -->
          <v-btn
            outlined
            rounded
            block
            color="white"
            class="google-btn"
            @click="signinWithGoogle"
          >
            <v-icon left color="red darken-1">mdi-google</v-icon>
            Sign in with Google
          </v-btn>
        </v-form>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
export default {
  layout: "auth",
  middleware: "guest",
  data() {
    return {
      name: '',
      email: ''
    }
  },
  methods: {
    signinWithGoogle() {
      this.$auth.loginWith('google')
    }
  }
}
</script>

<style scoped>
.signin-container {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  background: radial-gradient(circle at top left, #202020, #121212);
  padding: 24px;
}

.signin-card {
  border: 1px solid rgba(255, 255, 255, 0.05);
}

.divider-text {
  position: relative;
  text-align: center;
  font-size: 0.75rem;
  color: rgba(255, 255, 255, 0.4);
}

.divider-text span {
  background-color: #1c1c1e;
  padding: 0 10px;
  position: relative;
  z-index: 2;
}

.divider-text::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 0;
  right: 0;
  height: 1px;
  background: rgba(255, 255, 255, 0.1);
  z-index: 1;
}

.google-btn {
  font-weight: 500;
  text-transform: none;
  background-color: #ff0000;
  color: #000;
}
</style>
