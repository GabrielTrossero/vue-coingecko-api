<template>
  <div class="container">
    <div class="row">
      <h1>Coin Market</h1>

      <input
        type="text"
        class="form-control bg-dark text-light rounded-0 border-0 my-4"
        placeholder="Search Coin"
        @keyup="searchCoin"
        v-model="textSearch"
      />

      <table class="table table-dark">
        <thead>
          <tr>
            <th v-for="title in titles" :key="title">
              {{ title }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(coin, index) in filterCoins" :key="coin.id">
            <td>
              {{ index + 1 }}
            </td>
            <td>
              <img :src="coin.image" style="width: 2rem" class="me-2" />
              <span>
                {{ coin.name }}
              </span>
              <span class="ms-2 text-uppercase text-muted">
                {{ coin.symbol }}
              </span>
            </td>
            <td v-if="coin.current_price < 0">
              <span class="text-danger"> ${{ coin.current_price }} </span>
            </td>
            <td v-else>
              <span class="text-success"> ${{ coin.current_price }} </span>
            </td>
            <td>{{ coin.price_change_percentage_24h }} %</td>
            <td>${{ coin.total_volume.toLocaleString() }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
export default {
  name: "App",
  setup() {
    //coins tiene todo el listado de monedas, y filterCoins solo lo que se
    //va filtrando. Esto es para que si borramos el filtro, nos aparezcan
    //todas las monedas de nuevo
    const coins = ref(null);
    const filterCoins = ref(null);
    const textSearch = ref(null);

    onMounted(async () => {
      const res = await fetch(
        "https://api.coingecko.com/api/v3/coins/markets?vs_currency=USD&order=market_cap_desc&per_page=100&page=1&sparkline=false"
      );
      coins.value = await res.json();
      filterCoins.value = coins.value;
    });

    //funcion para filtrar el buscador
    const searchCoin = () => {
      filterCoins.value = coins.value.filter(
        (coin) =>
          coin.name.toLowerCase().includes(textSearch.value.toLowerCase()) ||
          coin.symbol.toLowerCase().includes(textSearch.value.toLowerCase())
      );
    };

    return {
      coins,
      filterCoins,
      titles: ["#", "Coin", "Price", "Price Change", "24h Volume"],
      searchCoin,
      textSearch,
    };
  },
};
</script>

<style></style>
