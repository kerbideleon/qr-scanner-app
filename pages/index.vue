<template>
  <v-container class="py-12">
    <!-- Header -->
    <v-row justify="center" class="mb-8">
      <v-col cols="12" class="text-center">
        <h1 class="text-h4 font-weight-bold mb-2">📸 Live QR Code Scanner</h1>
        <p class="text-body-1 text--secondary">
          Scan QR codes directly using your device's camera — fast, simple, and secure.
        </p>
      </v-col>
    </v-row>

    <!-- Action Button -->
    <v-row justify="center">
      <v-col cols="12" sm="6" md="4">
        <v-btn
          :loading="loading"
          color="deep-purple accent-4"
          class="mb-6"
          dark
          large
          block
          rounded
          @click="toggleScanner"
        >
          <v-icon left>mdi-camera</v-icon>
          {{ isScanning ? 'Stop Scanning' : 'Start QR Scanner' }}
        </v-btn>
      </v-col>
    </v-row>

    <!-- QR Reader Box -->
    <v-row justify="center">
      <v-col cols="12" sm="10" md="6">
        <v-sheet
          class="rounded-xl pa-6 d-flex align-center justify-center"
          color="#1e1e1e"
          elevation="6"
          style="min-height: 300px;"
        >
          <div id="qr-reader" class="w-100" style="height: 100%;"></div>
        </v-sheet>
      </v-col>
    </v-row>

    <!-- Scan Status / Error Message -->
    <v-row justify="center" class="mt-6">
      <v-col cols="12" sm="10" md="6">
        <v-alert
          v-if="status"
          :type="alertType"
          dense
          border="left"
          colored-border
          elevation="2"
        >
          {{ status }}
          <v-btn
            v-if="showRetry"
            text
            small
            color="amber darken-2"
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

<script>
export default {
  data: () => ({
    html5QrCode: null,
    isScanning: false,
    loading: false,
    status: '',
    alertType: 'info',
    showRetry: false
  }),

  mounted() {
    this.loadScannerLibrary()
  },

  beforeDestroy() {
    this.cleanupScanner()
  },

  methods: {
    async loadScannerLibrary() {
      try {
        const { Html5Qrcode } = await import('html5-qrcode')
        window.Html5Qrcode = Html5Qrcode
      } catch (error) {
        this.setStatus('⚠️ Unable to load QR scanner library.', 'error', true)
      }
    },

    async toggleScanner() {
      this.isScanning ? this.stopScanner() : await this.initScanner()
    },

    async initScanner() {
      this.loading = true
      this.setStatus('🔄 Initializing camera...', 'info')

      try {
        await this.testCamera()
        this.html5QrCode = new window.Html5Qrcode("qr-reader")

        await this.html5QrCode.start(
          { facingMode: "environment" },
          { fps: 10, qrbox: 250 },
          this.handleScan,
          () => {}
        )

        this.isScanning = true
        this.setStatus('✅ Ready to scan QR codes.', 'success')
      } catch (err) {
        this.handleError(err)
      } finally {
        this.loading = false
      }
    },

    async testCamera() {
      const stream = await navigator.mediaDevices.getUserMedia({
        video: { facingMode: "environment" }
      })
      stream.getTracks().forEach(track => track.stop())
    },

    handleScan(decodedText) {
      this.setStatus(`✅ QR Code: ${decodedText}`, 'success')
      this.$emit('scanned', decodedText)
    },

    handleError(error) {
      console.error('QR Scanner Error:', error)

      let message = 'Unexpected scanner error.'

      if (error.name === 'NotAllowedError') {
        message = '❌ Camera permission denied. Please allow access.'
      } else if (error.name === 'NotFoundError') {
        message = '📷 No compatible camera found.'
      } else if (error.message.includes('Requested device not found')) {
        message = '⚠️ Camera not available on this device or browser.'
      } else {
        message = error.message || message
      }

      this.setStatus(message, 'error', true)
      this.cleanupScanner()
    },

    setStatus(message, type, retry = false) {
      this.status = message
      this.alertType = type
      this.showRetry = retry
    },

    async stopScanner() {
      if (this.html5QrCode) {
        try {
          await this.html5QrCode.stop()
          this.setStatus('⏹ Scanner stopped.', 'info')
        } catch (err) {
          console.warn('Stopping error:', err)
        }
      }
      this.cleanupScanner()
    },

    cleanupScanner() {
      this.isScanning = false
      this.html5QrCode = null
      const container = document.getElementById('qr-reader')
      if (container) container.innerHTML = ''
    }
  }
}
</script>

<style scoped>
.text--secondary {
  opacity: 0.75;
}
</style>
