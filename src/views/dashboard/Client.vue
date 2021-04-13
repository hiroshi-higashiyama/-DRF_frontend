<template>
  <div class="page-clients">
    <nav class="breadcrumb" aria-label="breadcrumbs">
      <ul>
        <li><router-link to="/dashboard">ダッシュボード</router-link></li>
        <li><router-link to="/dashboard/clients">クライアント</router-link></li>
        <li class="is-active">
          <router-link
            :to="{ name: 'Client', params: { id: client.id } }"
            aria-current="true"
            >{{ client.name }}</router-link
          >
        </li>
      </ul>
    </nav>

    <div class="columns is-multiline">
      <div class="column is-12">
        <h1 class="title">{{ client.name }}</h1>

        <router-link
          :to="{ name: 'EditClient', params: { id: client.id } }"
          class="button is-light mt-4"
          >編集</router-link
        >
      </div>

      <div class="column is-12">
        <h2 class="subtitle">連絡先詳細</h2>

        <p>
          <strong>{{ client.name }}</strong>
        </p>
        <p v-if="client.address1">{{ client.address1 }}</p>
        <p v-if="client.address2">{{ client.address2 }}</p>
        <p v-if="client.zipcode || client.place">
          {{ client.zipcode }} {{ client.place }}
        </p>
        <p v-if="client.country">{{ client.country }}</p>
      </div>

      <div class="column is-12" v-if="unpaidInvoices.length">
        <div class="box">
          <h2 class="subtitle">未払い請求書</h2>

          <table class="table is-fullwidth">
            <thead>
              <tr>
                <th>#</th>
                <th>数量</th>
                <th>期日</th>
                <th></th>
              </tr>
            </thead>

            <tbody>
              <tr v-for="invoice in paidInvoices" v-bind:key="invoice.id">
                <td>{{ invoice.invoice_number }}</td>
                <td>{{ invoice.gross_amount }}</td>
                <td>{{ invoice.get_due_date_formatted }}</td>
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

      <div class="column is-12" v-if="paidInvoices.length">
        <div class="box">
          <h2 class="subtitle">支払い済の請求書</h2>

          <table class="table is-fullwidth">
            <thead>
              <tr>
                <th>#</th>
                <th>数量</th>
                <th>期日</th>
                <th></th>
              </tr>
            </thead>

            <tbody>
              <tr v-for="invoice in paidInvoices" v-bind:key="invoice.id">
                <td>{{ invoice.invoice_number }}</td>
                <td>{{ invoice.gross_amount }}</td>
                <td>{{ invoice.get_due_date_formatted }}</td>
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
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Client",
  data() {
    return {
      client: {
        invoices: [],
      },
    };
  },
  mounted() {
    this.getClient();
  },
  methods: {
    getClient() {
      const clientID = this.$route.params.id;

      axios
        .get(`/api/v1/clients/${clientID}`)
        .then((response) => {
          this.client = response.data;
        })
        .catch((error) => {
          console.log(JSON.stringify(error));
        });
    },
  },
  computed: {
    unpaidInvoices() {
      return this.client.invoices.filter(
        (invoice) => invoice.is_paid === false
      );
    },
    paidInvoices() {
      return this.client.invoices.filter((invoice) => invoice.is_paid === true);
    },
  },
};
</script>

<style>
</style>