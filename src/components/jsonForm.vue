<template>
  <b-form @submit.prevent="postApi">
    <div>
      <h5>Vložte vstupní data ve formátu JSON</h5>
      <b-form-Textarea
        :number="false"
        :value="inputData"
        type="text"
        v-model="inputData"
        outline
        style="font-family: consolas"
        rows="15"
      />
    </div>
    <b-btn type="submit" v-bind:disabled="working ? true : false">{{
      working ? "Zpracovávám..." : "Zpracovat data"
    }}</b-btn>
    <div>
      <h5>Výstup dat</h5>
      <b-form-Textarea
        :value="outputData"
        :number="false"
        type="text"
        outline
        style="font-family: consolas"
        rows="15"
        readonly
      />
    </div>
  </b-form>
</template>

<script>
import config from "../config.js";
import Vue from "vue";
import axios from "axios";
import VueAxios from "vue-axios";

Vue.use(VueAxios, axios);

export default {
  name: "jsonForm",
  components: {},
  data() {
    return {
      url: config.apiUrl,
      inputData: "",
      outputData: "",
      working: false,
    };
  },
  methods: {
    postApi() {
      this.working = true;
      axios
        .post(this.url, this.inputData, {
          headers: { "Content-Type": "application/json" },
        })
        .then((res) => {
         this.outputData = JSON.stringify(res.data.data);
        })
        .catch((err) => {
          this.outputData = err;
        })
        .finally(() => (this.working = false));
    },
  },
};
</script>
<style lang="scss" scoped>
</style>