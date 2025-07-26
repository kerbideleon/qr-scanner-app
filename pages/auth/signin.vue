<template>
  <div class="d-flex align-center justify-center signin-wrapper">
    <v-card
      max-width="440"
      class="pa-6 rounded-xl elevation-12"
      color="#1e1e1e"
    >
      <v-card-text>
        <!-- Branding / Header -->
        <div class="text-center mb-6">
          <h2 class="text-h5 font-weight-bold">🔐 Welcome Back</h2>
          <p class="text-caption text--secondary">
            Sign in to unlock your dashboard and QR scanner tools.
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
            prepend-inner-icon="mdi-account-circle"
            class="mb-4"
            color="deep-purple accent-4"
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
            color="deep-purple accent-4"
          />

          <v-btn
            color="deep-purple accent-4"
            class="mb-4 text-none"
            large
            block
            rounded
          >
            Sign In
          </v-btn>

          <!-- Divider -->
          <div class="my-4 text-center text--disabled text-caption">
            <span class="divider-line">or sign in with</span>
          </div>

          <!-- Google Sign-In -->
          <v-btn
            outlined
            rounded
            block
            color="white"
            elevation="1"
            class="google-btn text--dark"
            @click="signinWithGoogle"
          >
            <v-icon left color="red darken-1">mdi-google</v-icon>
            Continue with Google
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
.signin-wrapper {
  min-height: 100vh;
  padding: 32px;
  background: linear-gradient(135deg, #141414, #1e1e1e);
}

.google-btn {
  font-weight: 500;
  text-transform: none;
}

.divider-line {
  position: relative;
  display: inline-block;
  padding: 0 12px;
}

.divider-line::before,
.divider-line::after {
  content: '';
  position: absolute;
  top: 50%;
  width: 40px;
  height: 1px;
  background: rgba(255, 255, 255, 0.1);
}

.divider-line::before {
  left: -50px;
}

.divider-line::after {
  right: -50px;
}
</style>
