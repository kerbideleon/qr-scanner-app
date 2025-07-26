<template>
  <v-container class="scanner-container">
    <!-- Header -->
    <v-row justify="center" class="mb-8">
      <v-col cols="12" class="text-center">
        <h1 class="text-h4 font-weight-bold mb-2">🔍 QR Scanner</h1>
        <p class="text-subtitle-2 subtitle-muted">
          Activate your camera and scan QR codes in real-time.
        </p>
      </v-col>
    </v-row>

    <!-- Scan Button -->
    <v-row justify="center">
      <v-col cols="12" sm="6">
        <v-btn
          color="indigo darken-3"
          dark
          large
          block
          rounded
          class="mb-6"
          :loading="loading"
          @click="toggleScanner"
        >
          <v-icon left>mdi-qrcode-scan</v-icon>
          {{ isScanning ? 'Stop Scanning' : 'Start Scanner' }}
        </v-btn>
      </v-col>
    </v-row>

    <!-- QR Reader -->
    <v-row justify="center">
      <v-col cols="12" sm="6">
        <v-sheet
          class="pa-6 text-center rounded-xl qr-box"
          elevation="6"
          color="#1e1e1e"
        >
          <div id="qr-reader" style="width: 100%; height: 280px;"></div>
        </v-sheet>
      </v-col>
    </v-row>

    <!-- Status Feedback -->
    <v-row justify="center" class="mt-6">
      <v-col cols="12" sm="6">
        <v-alert
          v-if="status"
          :type="alertType"
          dense
          border="left"
          outlined
          colored-border
        >
          {{ status }}
          <v-btn
            v-if="showRetry"
            small
            text
            color="orange"
            class="ml-3"
            @click="initScanner"
          >
            Retry
          </v-btn>
        </v-alert>
      </v-col>
    </v-row>
  </v-container>
</template>
<style scoped>
.scanner-container {
  padding-top: 80px;
  padding-bottom: 80px;
}

.subtitle-muted {
  color: rgba(255, 255, 255, 0.6);
  font-size: 0.95rem;
}

.qr-box {
  border: 2px dashed rgba(255, 255, 255, 0.08);
  transition: all 0.3s ease;
}

.qr-box:hover {
  border-color: rgba(255, 255, 255, 0.2);
}
</style>
