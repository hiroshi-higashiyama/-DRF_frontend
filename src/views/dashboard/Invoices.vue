<template>
  <div class="page-invoices">
    <div class="columns is-multiline">
      <div class="column is-12">
        <h1 class="title">請求書</h1>
      </div>

      <div class="column is-12">
        <table class="table is-fullwidth">
          <thead>
            <tr>
              <th>#</th>
              <th>クライアント</th>
              <th>金額</th>
              <th>決済</th>
              <th></th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="invoice in invoices" v-bind:key="invoice.id">
              <td>{{ invoice.id }}</td>
              <td>{{ invoice.client_name }}</td>
              <td>{{ invoice.gross_amount }}</td>
              <td>{{ invoice.is_paid }}</td>
              <td>
                <router-link
                  :to="{ name: 'Invoice', params: { id: invoice.id } }"
                  >詳細</router-link
                >
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Invoices",
  data() {
    return {
      invoices: [],
    };
  },
  mounted() {
    this.getInvoices();
  },
  methods: {
    getInvoices() {
      axios
        .get("/api/v1/invoices/")
        .then((response) => {
          for (let i = 0; i < response.data.length; i++) {
            this.invoices.push(response.data[i]);
          }
        })
        .catch((error) => {
          console.log(JSON.stringify(error));
        });
    },
  },
};
</script>

<style>
</style>