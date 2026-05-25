<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar color="primary">
        <ion-title>Crypto App</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar color="primary">
          <ion-title size="large">Crypto App</ion-title>
        </ion-toolbar>
      </ion-header>

      <section class="hero">
        <p class="student">Ge Valent Nikolas - 054128848</p>
        <h1>Daftar Cryptocurrency Dunia</h1>
        <p>
          Data diambil dari API CoinLore dan menampilkan field
          <strong>rank</strong>, <strong>name</strong>, <strong>symbol</strong>,
          dan <strong>price_usd</strong>.
        </p>
        <ion-button expand="block" @click="ambilData" :disabled="loading">
          {{ loading ? 'Memuat data...' : 'Ambil Data Cryptocurrency' }}
        </ion-button>
      </section>

      <ion-text v-if="error" color="danger">
        <p class="message">{{ error }}</p>
      </ion-text>

      <ion-text v-if="!loading && !error && cryptos.length === 0" color="medium">
        <p class="message">Tekan tombol di atas untuk menampilkan data.</p>
      </ion-text>

      <ion-list v-if="loading">
        <ion-item v-for="index in 6" :key="index">
          <ion-label>
            <ion-skeleton-text :animated="true" class="skeleton-title"></ion-skeleton-text>
            <ion-skeleton-text :animated="true" class="skeleton-line"></ion-skeleton-text>
          </ion-label>
        </ion-item>
      </ion-list>

      <div v-if="!loading && cryptos.length > 0" class="table-wrapper">
        <table>
          <thead>
            <tr>
              <th>rank</th>
              <th>name</th>
              <th>symbol</th>
              <th>price_usd</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="crypto in cryptos" :key="crypto.id">
              <td>{{ crypto.rank }}</td>
              <td>{{ crypto.name }}</td>
              <td>{{ crypto.symbol }}</td>
              <td>${{ crypto.price_usd }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import {
  IonButton,
  IonContent,
  IonHeader,
  IonItem,
  IonLabel,
  IonList,
  IonPage,
  IonSkeletonText,
  IonText,
  IonTitle,
  IonToolbar,
} from '@ionic/vue'
import { defineComponent } from 'vue'

import EndPointAccess from '@/services/EndPointAccess'

interface CryptoCurrency {
  id: string
  rank: string
  name: string
  symbol: string
  price_usd: string
}

interface CoinLoreResponse {
  data: CryptoCurrency[]
}

export default defineComponent({
  name: 'Home',
  data() {
    return {
      cryptos: [] as CryptoCurrency[],
      loading: false,
      error: '',
    }
  },
  mounted() {
    this.ambilData()
  },
  methods: {
    async ambilData() {
      this.loading = true
      this.error = ''

      try {
        const endpoint = new EndPointAccess('https://api.coinlore.net/api/tickers/')
        const response = await endpoint.getRes()
        const result = response.data as CoinLoreResponse

        this.cryptos = result.data.map((crypto) => ({
          id: crypto.id,
          rank: crypto.rank,
          name: crypto.name,
          symbol: crypto.symbol,
          price_usd: crypto.price_usd,
        }))
      } catch (err) {
        this.error = err instanceof Error
          ? `Gagal mengambil data: ${err.message}`
          : 'Gagal mengambil data cryptocurrency.'
      } finally {
        this.loading = false
      }
    },
  },
  components: {
    IonButton,
    IonContent,
    IonHeader,
    IonItem,
    IonLabel,
    IonList,
    IonPage,
    IonSkeletonText,
    IonText,
    IonTitle,
    IonToolbar,
  },
})
</script>

<style scoped>
.hero {
  padding: 24px 18px 18px;
  text-align: center;
}

.hero h1 {
  color: var(--ion-color-primary);
  font-size: 1.6rem;
  margin: 8px 0;
}

.hero p {
  color: var(--ion-color-medium);
  line-height: 1.5;
}

.student {
  font-size: 0.9rem;
  margin: 0;
}

.message {
  padding: 0 18px 18px;
  text-align: center;
}

.table-wrapper {
  overflow-x: auto;
  padding: 0 12px 24px;
}

table {
  background: #ffffff;
  border-collapse: collapse;
  border-radius: 14px;
  box-shadow: 0 8px 28px rgba(0, 0, 0, 0.08);
  min-width: 100%;
  overflow: hidden;
}

th,
td {
  border-bottom: 1px solid #e9edf3;
  padding: 12px 10px;
  text-align: left;
  white-space: nowrap;
}

th {
  background: var(--ion-color-primary);
  color: #ffffff;
  font-size: 0.8rem;
  text-transform: lowercase;
}

td:first-child {
  color: var(--ion-color-primary);
  font-weight: 700;
}

tr:last-child td {
  border-bottom: 0;
}

.skeleton-title {
  height: 18px;
  width: 45%;
}

.skeleton-line {
  height: 14px;
  width: 80%;
}
</style>
