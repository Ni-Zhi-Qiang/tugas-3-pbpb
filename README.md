# Crypto App

Aplikasi mobile berbasis Ionic Vue untuk menampilkan daftar cryptocurrency dari API CoinLore.

## Informasi Mahasiswa

| | |
|---|---|
| Nama | Ge Valent Nikolas |
| NIM | 054128848 |

## Kesesuaian Tugas

Aplikasi mengambil data dari endpoint:

```text
https://api.coinlore.net/api/tickers/
```

Field yang ditampilkan pada layar:

- `rank`
- `name`
- `symbol`
- `price_usd`

## File yang Dibuat

- `src/main.ts`: file utama aplikasi. File ini membuat instance Vue, mengaktifkan Ionic Vue, memasang router, dan memuat CSS Ionic serta tema.
- `src/App.vue`: komponen utama yang berisi `ion-app` dan `ion-router-outlet`.
- `src/router/index.ts`: mendefinisikan route `/home` sebagai halaman utama aplikasi.
- `src/services/EndPointAccess/index.ts`: class service untuk mengakses endpoint REST API menggunakan `axios`.
- `src/views/Home.vue`: halaman utama yang mengambil data CoinLore dan menampilkan tabel cryptocurrency.
- `src/theme/variables.css`: pengaturan warna tema Ionic.
- `index.html`: dokumen HTML utama yang memuat `src/main.ts`.

## Cara Menjalankan

```sh
npm install
npm run dev
```

Setelah server berjalan, buka URL yang ditampilkan oleh Ionic, biasanya:

```text
http://localhost:8100/
```

## Ringkasan Source Code Utama

Pada `src/views/Home.vue`, method `ambilData()` membuat instance `EndPointAccess`, mengambil data dari API CoinLore, lalu menyimpan hasilnya ke `cryptos` agar dapat ditampilkan dengan `v-for`.

```ts
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
```
