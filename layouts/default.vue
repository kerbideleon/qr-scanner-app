<template>
  <v-app dark>
    <!-- Custom App Bar -->
    <v-app-bar app flat color="#121212" class="elevation-2">
      <v-row class="w-100 align-center">
        <v-col>
          <div class="ml-4 font-weight-bold text-subtitle-1 text-white">
            QR Code Scanner
          </div>
        </v-col>
        <v-col class="d-flex justify-end">
          <v-btn
            color="blue darken-2"
            dark
            small
            rounded
            class="mr-4"
            @click="$auth.logout()"
          >
            <v-icon left small>mdi-logout</v-icon>
            Logout
          </v-btn>
        </v-col>
      </v-row>
    </v-app-bar>

    <!-- Page Content -->
    <v-main>
      <Nuxt />
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  middleware: ['auth']
}
</script>

<style scoped>
.v-app-bar {
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
}
</style>
