<template>
  <v-container class="py-10">
    <!-- Title -->
    <v-row justify="center" class="mb-6">
      <v-col cols="12" class="text-center">
        <h1 class="text-h5 font-weight-bold">🔍 QR Scanner Portal</h1>
        <p class="text-subtitle-1 text-grey lighten-1">
          Activate your camera and scan any QR code in real-time.
        </p>
      </v-col>
    </v-row>

    <!-- Scan Button -->
    <v-row justify="center">
      <v-col cols="12" sm="6">
        <v-btn
          color="teal darken-2"
          dark
          large
          block
          rounded
          class="mb-4"
          :loading="loading"
          @click="toggleScanner"
        >
          <v-icon left>mdi-qrcode</v-icon>
          {{ isScanning ? 'Stop Camera' : 'Start QR Scan' }}
        </v-btn>
      </v-col>
    </v-row>

    <!-- QR Reader Container -->
    <v-row justify="center">
      <v-col cols="12" sm="6">
        <v-sheet
          class="pa-6 text-center rounded-lg"
          elevation="4"
          color="#121212"
          style="min-height: 280px;"
        >
          <div id="qr-reader" style="width: 100%; height: 100%"></div>
        </v-sheet>
      </v-col>
    </v-row>

    <!-- Feedback / Status -->
    <v-row justify="center" class="mt-5">
      <v-col cols="12" sm="6">
        <v-alert
          v-if="status"
          :type="alertType"
          border="left"
          dense
          outlined
        >
          {{ status }}
          <v-btn
            v-if="showRetry"
            small
            text
            color="orange"
            class="ml-2"
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

  async mounted() {
    await this.loadLibrary()
  },

  beforeDestroy() {
    this.cleanupScanner()
  },

  methods: {
    async loadLibrary() {
      try {
        const { Html5Qrcode } = await import('html5-qrcode')
        window.Html5Qrcode = Html5Qrcode
      } catch (err) {
        this.setStatus('Failed to load scanner library.', 'error', true)
      }
    },

    async toggleScanner() {
      this.isScanning ? this.stopScanner() : await this.initScanner()
    },

    async initScanner() {
      this.loading = true
      this.setStatus('Launching scanner...', 'info')

      try {
        await this.testCameraAccess()
        this.html5QrCode = new window.Html5Qrcode("qr-reader")

        await this.html5QrCode.start(
          { facingMode: "environment" },
          { fps: 10, qrbox: 250 },
          this.handleScan,
          () => {}
        )

        this.isScanning = true
        this.setStatus('Ready to scan QR code.', 'success')
      } catch (err) {
        this.handleError(err)
      } finally {
        this.loading = false
      }
    },

    async testCameraAccess() {
      const stream = await navigator.mediaDevices.getUserMedia({
        video: {
          facingMode: "environment",
          width: { ideal: 1280 },
          height: { ideal: 720 }
        }
      })
      stream.getTracks().forEach(track => track.stop())
    },

    handleScan(decodedText) {
      this.setStatus(`✅ Scanned: ${decodedText}`, 'success')
      this.$emit('scanned', decodedText)
    },

    handleError(err) {
      console.error('Scanner error:', err)
      let message = 'Unexpected scanner issue.'

      if (err.name === 'NotAllowedError') {
        message = 'Camera access denied. Please allow camera permissions.'
      } else if (err.name === 'NotFoundError') {
        message = 'No camera found on this device.'
      } else if (err.message.includes('Requested device not found')) {
        message = 'Camera unavailable. Try another browser/device.'
      } else {
        message = err.message || 'Unknown scanning error.'
      }

      this.setStatus(message, 'error', true)
      this.cleanupScanner()
    },

    setStatus(text, type, showRetry = false) {
      this.status = text
      this.alertType = type
      this.showRetry = showRetry
    },

    async stopScanner() {
      if (this.html5QrCode) {
        try {
          await this.html5QrCode.stop()
          this.setStatus('Scanner stopped.', 'info')
        } catch (err) {
          console.warn('Error stopping scanner:', err)
        }
      }
      this.cleanupScanner()
    },

    cleanupScanner() {
      this.isScanning = false
      this.html5QrCode = null
      const el = document.getElementById('qr-reader')
      if (el) el.innerHTML = ''
    }
  }
}
</script>

<style scoped>
.text-subtitle-1 {
  opacity: 0.7;
}
</style>
