<template>
  <div class="page-dashboard">
    <div class="columns is-multiline">
      <div class="column is-12">
        <h1 class="title">ダッシュボード</h1>
      </div>

      <div class="column is-6">
        <div class="box">
          <h2 class="subtitle">未払いの請求書</h2>

          <table class="table is-fullwidth">
            <thead>
              <tr>
                <th>#</th>
                <th>クライアント</th>
                <th>数量</th>
                <th>期日</th>
                <th></th>
              </tr>
            </thead>

            <tbody>
              <tr v-for="invoice in unpaidInvoices" v-bind:key="invoice.id">
                <td>{{ invoice.invoice_number }}</td>
                <td>{{ invoice.client_name }}</td>
                <td>{{ invoice.gross_amount }}</td>
                <td>{{ invoice.get_due_date_formatted }}</td>
                <td>
                  <router-link
                    :to="{ name: 'Invoice', params: { id: invoice.id } }"
                    >Details</router-link
                  >
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <div class="column is-6">
        <div class="box">
          <h2 class="subtitle">最新のクライアント</h2>

          <div v-for="client in clients" v-bind:key="client.id">
            <div class="box mb-2">
              <h3 class="is-size-4 mb-4">{{ client.name }}</h3>

              <router-link
                :to="{ name: 'Client', params: { id: client.id } }"
                class="button is-light"
                >詳細</router-link
              >
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Dashboard",
  data() {
    return {
      invoices: [],
      clients: [],
    };
  },
  mounted() {
    this.getInvoices();
    this.getClients();
  },
  methods: {
    getInvoices() {
      axios
        .get("/api/v1/invoices/")
        .then((response) => {
          for (let i = 0; i < response.data.length; i++) {
            if (!response.data[i].is_credit_for) {
              this.invoices.push(response.data[i]);
            }
          }
        })
        .catch((error) => {
          console.log(JSON.stringify(error));
        });
    },
    getClients() {
      axios
        .get("/api/v1/clients/")
        .then((response) => {
          for (let i = 0; i < response.data.length; i++) {
            if (this.clients.length <= 5) {
              this.clients.push(response.data[i]);
            }
          }
        })
        .catch((error) => {
          console.log(JSON.stringify(error));
        });
    },
  },
  computed: {
    unpaidInvoices() {
      return this.invoices.filter((invoice) => invoice.is_paid === false);
    },
  },
};
</script>