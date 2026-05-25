<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar color="primary">
        <ion-title>Crypto App</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" class="page-background">
      <main class="app-shell">
        <section class="intro-card">
          <p class="eyebrow">Tugas 03</p>
          <h1>Daftar Cryptocurrency Dunia</h1>
          <p class="description">
            Aplikasi ini mengambil data dari CoinLore API dan menampilkan field
            <strong>rank</strong>, <strong>name</strong>, <strong>symbol</strong>,
            dan <strong>price_usd</strong>.
          </p>

          <div class="action-row">
            <ion-button class="refresh-button" @click="ambilData" :disabled="loading">
              {{ loading ? 'Memuat...' : 'Refresh' }}
            </ion-button>
            <span class="data-count">{{ cryptos.length }} data</span>
          </div>
        </section>

        <section class="data-card">
          <header class="data-header">
            <div>
              <p class="eyebrow">CoinLore Tickers</p>
              <h2>Harga Crypto</h2>
            </div>
            <span class="api-badge">Online API</span>
          </header>

          <p v-if="error" class="state-message error-message">{{ error }}</p>
          <p v-else-if="loading" class="state-message">Memuat data cryptocurrency...</p>
          <p v-else-if="cryptos.length === 0" class="state-message">
            Tekan Refresh untuk memuat data.
          </p>

          <div v-else class="crypto-list">
            <article v-for="crypto in cryptos" :key="crypto.id" class="crypto-row">
              <div class="rank-column">
                <span>Rank</span>
                <strong>{{ crypto.rank }}</strong>
              </div>

              <div class="name-column">
                <span>{{ crypto.name }}</span>
                <strong>{{ crypto.symbol }}</strong>
              </div>

              <div class="price-column">
                <span>USD</span>
                <strong>{{ crypto.price_usd }}</strong>
              </div>
            </article>
          </div>
        </section>
      </main>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { IonButton, IonContent, IonHeader, IonPage, IonTitle, IonToolbar } from '@ionic/vue'
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
    IonPage,
    IonTitle,
    IonToolbar,
  },
})
</script>

<style scoped>
.page-background::part(background) {
  background: #eef3ff;
}

.app-shell {
  display: grid;
  gap: 18px;
  margin: 0 auto;
  max-width: 1120px;
  padding: clamp(14px, 4vw, 36px);
  width: 100%;
}

.intro-card,
.data-card {
  background: #ffffff;
  border: 1px solid rgba(109, 136, 189, 0.22);
  border-radius: 22px;
  box-shadow: 0 16px 45px rgba(31, 63, 118, 0.12);
}

.intro-card {
  padding: clamp(20px, 5vw, 34px);
}

.eyebrow {
  color: #4f6fa8;
  font-size: 0.76rem;
  font-weight: 700;
  letter-spacing: 0.08em;
  margin: 0;
  text-transform: uppercase;
}

.intro-card h1,
.data-header h2 {
  color: #14213d;
  margin: 8px 0;
}

.intro-card h1 {
  font-size: clamp(1.65rem, 5vw, 2.8rem);
  line-height: 1.05;
}

.description {
  color: #43516b;
  line-height: 1.6;
  margin: 0 0 20px;
}

.action-row {
  align-items: center;
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
}

.refresh-button {
  --background: #0058e6;
  --border-radius: 10px;
  --box-shadow: 0 8px 18px rgba(0, 88, 230, 0.22);
  font-weight: 700;
  min-height: 42px;
  text-transform: none;
}

.data-count,
.api-badge {
  background: #eef3ff;
  border-radius: 999px;
  color: #34517e;
  font-size: 0.82rem;
  font-weight: 700;
  padding: 8px 12px;
}

.data-card {
  overflow: hidden;
}

.data-header {
  align-items: center;
  background: #ffffff;
  display: flex;
  justify-content: space-between;
  gap: 12px;
  padding: 18px;
}

.data-header h2 {
  font-size: 1.25rem;
}

.crypto-list {
  border-top: 1px solid #d3bb73;
}

.crypto-row {
  --row-background: #fff0bf;

  align-items: center;
  background: var(--row-background);
  border-bottom: 1px solid #d3bb73;
  display: grid;
  gap: clamp(8px, 2vw, 18px);
  grid-template-columns: minmax(52px, 0.35fr) minmax(0, 1fr) minmax(88px, 0.7fr);
  min-height: 62px;
  padding: clamp(10px, 2.3vw, 16px);
}

.rank-column,
.name-column,
.price-column {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.rank-column {
  align-items: center;
}

.rank-column span,
.name-column span,
.price-column span {
  color: #000000;
  font-size: clamp(0.66rem, 1.7vw, 0.8rem);
  line-height: 1.1;
}

.rank-column strong {
  color: #000000;
  font-size: clamp(1.35rem, 4vw, 1.65rem);
  font-weight: 400;
  line-height: 1.1;
}

.name-column strong,
.price-column strong {
  color: #000000;
  font-size: clamp(1.05rem, 3.5vw, 1.4rem);
  font-weight: 500;
  line-height: 1.1;
}

.name-column span {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.price-column {
  padding-left: 4px;
}

.state-message {
  color: #333333;
  font-size: 0.95rem;
  margin: 28px 18px;
  text-align: center;
}

.error-message {
  color: #c5000f;
}

@media (max-width: 520px) {
  .app-shell {
    gap: 12px;
    padding: 10px;
  }

  .intro-card,
  .data-card {
    border-radius: 16px;
  }

  .data-header {
    align-items: flex-start;
    flex-direction: column;
  }

  .crypto-row {
    grid-template-columns: 52px minmax(0, 1fr) minmax(82px, 0.7fr);
    min-height: 56px;
  }
}

@media (min-width: 720px) {
  .app-shell {
    max-width: 760px;
  }
}

@media (min-width: 1024px) {
  .app-shell {
    grid-template-columns: minmax(280px, 360px) minmax(0, 1fr);
    max-width: 1120px;
  }

  .intro-card {
    position: sticky;
    top: 24px;
  }

  .crypto-list {
    max-height: calc(100vh - 190px);
    overflow: auto;
  }
}
</style>
