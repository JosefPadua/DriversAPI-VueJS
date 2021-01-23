<template>
  <div id="app">
    <section style="background: #ededed; padding-bottom: 100px">
      <!-- Purple Header -->
      <div class="edge-header deep-purple" />

      <!-- Card Container -->
      <b-container class="free-bird">
        <b-row>
          <b-col
            md="8"
            lg="7"
            class="mx-auto float-none white z-depth-1 py-2 px-2"
          >
            <b-card-body>
              <h2 class="h2-responsive">
                <strong>Analytický nástroj pro výstup dat řidičů</strong>
              </h2>
              <p class="pb-4">Slouží jen jako ukázková aplikace</p>

              <!--Body-->
              <form>
                <!-- Switch -->
                <h5>Vyberte mód výstupních dat</h5>
                <div class="custom-control custom-switch">
                  <input
                    type="checkbox"
                    class="custom-control-input"
                    id="customSwitches"
                    v-model="mode"
                  />
                  <label class="custom-control-label" for="customSwitches">{{
                    mode ? "Vizualní" : "JSON"
                  }}</label>
                </div>
              </form>
              <div class="page-content">
                <jsonForm v-if="!mode" />
                <visualForm v-if="mode" />
              </div>
              <p>
                <small
                  >Zpracování dat může nějakou chvíli trvat, proto Vás prosíme o
                  trpělivost.</small
                >
              </p>
            </b-card-body>
          </b-col>
        </b-row>
      </b-container>
      <!-- /.Card Container -->
    </section>
  </div>
</template>

<script>
import jsonForm from "./components/jsonForm";
import visualForm from "./components/visualForm";

export default {
  name: "App",
  components: {
    jsonForm,
    visualForm,
  },
  data() {
    return {
      mode: false,
    };
  },
};
</script>

<style lang="scss">
@import "node_modules/bootstrap/scss/bootstrap.scss";
@import "node_modules/bootstrap-vue/src/index.scss";

.page-content {
  margin-top: 32px;
}
</style>