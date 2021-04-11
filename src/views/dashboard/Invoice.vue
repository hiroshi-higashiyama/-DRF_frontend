<template>
  <div class="page-invoices">
    <nav class="breadcrumb" aria-label="breadcrumbs">
      <ul>
        <li><router-link to="/dashboard">ダッシュボード</router-link></li>
        <li><router-link to="/dashboard/invoices">請求書</router-link></li>
        <li class="is-active">
          <router-link
            :to="{ name: 'Invoice', params: { id: invoice.id } }"
            aria-current="true"
            >{{ invoice.invoice_number }}</router-link
          >
        </li>
      </ul>
    </nav>

    <div class="columns is-multiline">
      <div class="column is-12">
        <h1 class="title">請求書 - {{ invoice.invoice_number }}</h1>

        <div class="buttons">
          <button @click="getPdf()" class="button is-dark">Download PDF</button>
          <button
            @click="setAsPaid()"
            class="button is-success"
            v-if="!invoice.is_paid"
          >
            支払完了
          </button>
        </div>
      </div>

      <div class="column is-12 mb-4">
        <div class="box">
          <h3 class="is-size-4 mb-5">クライアント</h3>

          <p>
            <strong>{{ invoice.client_name }}</strong>
          </p>
          <p v-if="invoice.client_address1">{{ invoice.client_address1 }}</p>
          <p v-if="invoice.client_address2">{{ invoice.client_address2 }}</p>
          <p v-if="invoice.client_zipcode || invoice.client_place">
            {{ invoice.client_zipcode }} {{ invoice.client_place }}
          </p>
          <p v-if="invoice.client_country">{{ invoice.client_country }}</p>
        </div>
      </div>

      <div class="column is-12 mb-4">
        <div class="box">
          <h3 class="is-size-4 mb-5">商品</h3>

          <table class="table is-fullwidth">
            <thead>
              <tr>
                <th>商品名</th>
                <th>数量</th>
                <th>消費税</th>
                <th>合計金額</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in invoice.items" v-bind:key="item.id">
                <td>{{ item.title }}</td>
                <td>{{ item.quantity }}</td>
                <td>{{ item.vat_rate }}%</td>
                <td>{{ getItemTotal(item) }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <div class="column is-12">
        <div class="box">
          <h3 class="is-size-4 mb-5">概要</h3>

          <div class="columns">
            <div class="column is-6">
              <p><strong>金額</strong>: {{ invoice.net_amount }}</p>
              <p><strong>消費税</strong>: {{ invoice.vat_amount }}</p>
              <p><strong>総額</strong>: {{ invoice.gross_amount }}</p>
              <p><strong>銀行口座</strong>: {{ invoice.bankaccount }}</p>
            </div>

            <div class="column is-6">
              <p><strong>参照</strong>: {{ invoice.sender_reference }}</p>
              <p>
                <strong>クライアント参照</strong>:
                {{ invoice.client_contact_reference }}
              </p>
              <p><strong>期日</strong>: {{ invoice.get_due_date_formatted }}</p>
              <p><strong>決済</strong>: {{ getStatusLabel() }}</p>
              <p><strong>タイプ</strong>: {{ getInvoiceType() }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { toast } from "bulma-toast";

const fileDownload = require("js-file-download");

export default {
  name: "Invoice",
  data() {
    return {
      invoice: {},
      items: [],
    };
  },
  mounted() {
    this.getInvoice();
  },
  methods: {
    getInvoice() {
      const invoiceID = this.$route.params.id;

      axios
        .get(`/api/v1/invoices/${invoiceID}`)
        .then((response) => {
          console.log(response);
          this.invoice = response.data;
        })
        .catch((error) => {
          console.log(JSON.stringify(error));
        });
    },
    getPdf() {
      const invoiceID = this.$route.params.id;

      axios
        .get(`/api/v1/invoices/${invoiceID}/generate_pdf/`, {
          responseType: "blob",
        })
        .then((res) => {
          fileDownload(res.data, `invoice_${invoiceID}.pdf`);
        })
        .catch((err) => {
          console.log(err);
        });
    },
    getStatusLabel() {
      if (this.invoice.is_paid) {
        return "済";
      } else {
        return "未";
      }
    },
    getInvoiceType() {
      if (this.invoice.invoice_type === "credit_note") {
        return "クレジットノート";
      } else {
        return "請求書";
      }
    },
    getItemTotal(item) {
      const unit_price = item.unit_price;
      const quantity = item.quantity;
      const total = item.net_amount + item.net_amount * (item.vat_rate / 100);
      return parseFloat(total).toFixed(2);
    },
    async setAsPaid() {
      this.invoice.is_paid = true;

      let items = this.invoice.items;

      delete this.invoice["items"];

      await axios
        .patch(`/api/v1/invoices/${this.invoice.id}/`, this.invoice)
        .then((response) => {
          toast({
            message: "変更内容を保存",
            type: "is-success",
            dismissible: true,
            pauseOnHover: true,
            duration: 3000,
            position: "bottom-right",
          });
        })
        .catch((error) => {
          console.log(JSON.stringify(error));
        });
        this.invoice.items = items;
    },
  },
};
</script>