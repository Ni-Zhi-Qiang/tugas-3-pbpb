<template>
  <ion-page>
    <ion-content :fullscreen="true" class="page-background">
      <div class="phone-frame">
        <div class="phone-screen">
          <div class="toolbar-area">
            <ion-button class="refresh-button" @click="ambilData" :disabled="loading">
              {{ loading ? 'Loading' : 'Refresh' }}
            </ion-button>
          </div>

          <p v-if="error" class="state-message error-message">{{ error }}</p>
          <p v-else-if="loading" class="state-message">Memuat data...</p>
          <p v-else-if="cryptos.length === 0" class="state-message">
            Tekan Refresh untuk memuat data.
          </p>

          <div v-else class="crypto-list">
            <article v-for="crypto in displayedCryptos" :key="crypto.id" class="crypto-row">
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
        </div>

        <div class="home-indicator"></div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { IonButton, IonContent, IonPage } from '@ionic/vue'
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
  computed: {
    displayedCryptos(): CryptoCurrency[] {
      return this.cryptos.slice(0, 7)
    },
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
    IonPage,
  },
})
</script>

<style scoped>
.page-background::part(background) {
  background: #ffffff;
}

.phone-frame {
  background: #cfcfcf;
  border: 8px solid #6d88bd;
  border-radius: 48px;
  box-sizing: border-box;
  margin: 28px auto;
  min-height: 430px;
  padding: 34px 8px 36px;
  position: relative;
  width: 260px;
}

.phone-screen {
  background: #ffffff;
  border: 1px solid #d4d4d4;
  min-height: 342px;
  overflow: hidden;
}

.toolbar-area {
  align-items: center;
  display: flex;
  height: 48px;
  justify-content: center;
}

.refresh-button {
  --background: #0058e6;
  --border-radius: 5px;
  --box-shadow: none;
  font-size: 10px;
  height: 30px;
  margin: 0;
  min-height: 30px;
  text-transform: none;
  width: 70px;
}

.crypto-list {
  border-top: 1px solid #d3bb73;
}

.crypto-row {
  align-items: center;
  background: #fff0bf;
  border-bottom: 1px solid #d3bb73;
  display: grid;
  grid-template-columns: 52px 1fr 92px;
  min-height: 42px;
  padding: 3px 8px;
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
  font-size: 8px;
  line-height: 1.1;
}

.rank-column strong {
  color: #000000;
  font-size: 17px;
  font-weight: 400;
  line-height: 1.1;
}

.name-column strong,
.price-column strong {
  color: #000000;
  font-size: 16px;
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
  font-size: 12px;
  margin: 24px 12px;
  text-align: center;
}

.error-message {
  color: #c5000f;
}

.home-indicator {
  background: #ffffff;
  border-radius: 999px;
  bottom: 14px;
  height: 15px;
  left: 50%;
  position: absolute;
  transform: translateX(-50%);
  width: 60px;
}
</style>
