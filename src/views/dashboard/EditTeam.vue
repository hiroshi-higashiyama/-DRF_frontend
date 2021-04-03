<template>
  <div class="page-edit-team">
    <div class="columns is-multiline">
      <div class="column is-12">
        <h1 class="title">チーム編集</h1>
      </div>

      <div class="column is-12">
        <div class="field">
          <label>Name</label>
          <div class="control">
            <input type="text" class="input" v-model="team.name" />
          </div>
        </div>

        <div class="field">
          <label>Org. number</label>
          <div class="control">
            <input type="text" class="input" v-model="team.org_number" />
          </div>
        </div>

        <div class="field">
          <label>First invoice number</label>
          <div class="control">
            <input
              type="number"
              class="input"
              v-model="team.first_invoice_number"
            />
          </div>
        </div>

        <div class="field">
          <div class="control">
            <button class="button is-success" @click="submitFrom">保存</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { toast } from "bulma-toast";

export default {
  name: "EditTeam",
  data() {
    return {
      team: {},
    };
  },
  async mounted() {
    await this.getOrCreateTeam();
  },
  methods: {
    getOrCreateTeam() {
      axios
        .get("/api/v1/teams/")
        .then((response) => (this.team = response.data[0]))
        .catch((error) => {
          console.log(JSON.stringify(error));
        });
    },
    submitFrom() {
      axios
        .patch(`api/v1/teams/${this.team.id}/`, this.team)
        .then((response) => {
          toast({
            message: "変更内容を保存",
            type: "is-success",
            dismissible: true,
            pauseOnHover: true,
            duration: 3000,
            position: "bottom-right",
          });

          this.$router.push("/dashboard/my-account");
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