<template>
  <div class="bitcoin">
    <h1>Bitcoin Price Index</h1>

    <section v-if="errored">
      <p>We're sorry, we're not able to retrieve this information at the moment, please try back later</p>
    </section>

    <div v-else class="row justify-content-center">
      <section class="col-5">
        <b-card>
          <div v-if="loading">
            <b-spinner label="Loading..."></b-spinner>
          </div>

          <div v-if="!loading">
            <div v-for="currency in info" :key="currency">
              {{ currency.description }}:
              <span class="lighten">
                <span v-html="currency.symbol"></span>
                {{ currency.rate_float | currencydecimal }}
              </span>
            </div>
          </div>
        </b-card>
      </section>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "bitcoin",
  data() {
    return {
      info: null,
      loading: true,
      errored: false
    };
  },
  filters: {
    currencydecimal(value) {
      return value.toFixed(2);
    }
  },
  methods: {
   async fetch() {
      this.loading = true;
      axios
        .get("https://api.coindesk.com/v1/bpi/currentprice.json")
        .then(response => {
          this.info = response.data.bpi;
        })
        .catch(error => {
          console.log(error);
          this.errored = true;
        })
        .finally(() => {
          this.loading = false;
        });
    }
  },
  mounted() {
    this.fetch();
  }
};
</script>