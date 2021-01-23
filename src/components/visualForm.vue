<template>
  <div>
    <h5>Řidiči (vstupní data)</h5>
    <!-- Koncept: MOŽNOST IMPORTU A EXPOTU JSON VSTUPU/VÝSTUPU -->
    <!-- <h6>Importovat z JSON zdroje</h6> -->
    <!-- <b-row>
      <b-col cols="8">
        <b-form-file
          accept=".json"
          v-model="importedFile"
          :file-name-formatter="formatNames"
        ></b-form-file>
      </b-col>
      <b-col>
        <b-btn size="sm" @click.prevent="importFile">importovat</b-btn>
      </b-col>
    </b-row> -->

    <b-card-group deck style="margin-top: 10px">
      <a @click="(modalform = !modalform), (editing = false), clearForm()">
        <div class="h-100">
          <b-card class="text-center w-100 h-100">
            <b-card-text>+ Přidat řidiče</b-card-text>
          </b-card>
        </div>
      </a>
      <div
        class="w-25"
        v-for="(drivera, i) in drivers"
        :key="i"
        style="display: inline-block"
      >
        <b-card stretched-link class="text-center w-100 driver" :class="outputData ? outputData.driversFilter1.includes(drivera.id) ? 'border border-success' : null || outputData.driversFilter2.includes(drivera.id) ? 'border border-danger' :null : null">
          <header>
            {{ drivera.driver.firstName }}
            <div
              :class="
                outputData.sameLastnames
                  ? Object.keys(outputData.sameLastnames).includes(
                      drivera.driver.lastName
                    )
                    ? 'yellow'
                    : null
                  : null
              "
            >
              {{ drivera.driver.lastName }}
            </div>
          </header>
          <b-card-text
            >{{ drivera.driver.company }}<br />Věk:
            {{ getAge(drivera.driver.birthDate) }}<br />
            {{ drivera.vehicleInfo.length }} vozidel</b-card-text
          >
          <b-row>
            <b-col>
              <a
                class="text-primary"
                @click="
                  (modalform = !modalform),
                    (editing = true),
                    startEditDriver(drivera.id)
                "
                >Upravit</a
              >
            </b-col>
            <b-col>
              <a class="text-danger" @click="removeDriver(drivera.id)"
                >Odstranit</a
              >
            </b-col>
          </b-row>
        </b-card>
      </div>
    </b-card-group>
    <b-modal
      :title="editing ? 'Úprava řidiče' : 'Přidání řidiče'"
      class="modal fade"
      v-model="modalform"
      @ok.prevent="editing ? editDriver() : addDriver()"
    >
      <div class="modal-dialog-centered" role="document">
        <b-form @submit.prevent="addDriver">
          <b-row>
            <b-col sm="3">
              <label for="input-small">Jméno(-a)</label>
            </b-col>
            <b-col sm="9">
              <b-form-input
                v-model="currentDriver.driver.firstName"
                required
                placeholder="Vložte křestní jméno/jména"
              ></b-form-input>
            </b-col>
          </b-row>
          <b-row>
            <b-col sm="3">
              <label for="input-small">Příjmení</label>
            </b-col>
            <b-col sm="9">
              <b-form-input
                v-model="currentDriver.driver.lastName"
                required
                placeholder="Vložte přijmení"
              ></b-form-input>
            </b-col>
          </b-row>
          <b-row>
            <b-col sm="3">
              <label for="input-small">Barva očí</label>
            </b-col>
            <b-col sm="9">
              <b-form-select
                required
                v-model="currentDriver.driver.eyeColor"
                :options="eyeColors"
                class="mt-3"
              ></b-form-select>
            </b-col>
          </b-row>
          <b-row>
            <b-col sm="3">
              <label for="input-small">Společnost</label>
            </b-col>
            <b-col sm="9">
              <b-form-input
                v-model="currentDriver.driver.company"
                required
                placeholder="Vložte společnost, pro kterou řidič jezdí"
              ></b-form-input>
            </b-col>
          </b-row>
          <b-row>
            <b-col sm="3">
              <label for="input-small">Město</label>
            </b-col>
            <b-col sm="9">
              <b-form-input
                required
                placeholder="Vložte město, kde řidič má trvalý pobyt"
              ></b-form-input>
            </b-col>
          </b-row>
          <b-row>
            <b-col sm="3">
              <label for="input-small">Datum narození</label>
            </b-col>
            <b-col sm="9">
              <b-form-datepicker
                v-model="currentDriver.driver.birthDate"
                class="mb-9"
              ></b-form-datepicker>
            </b-col>
          </b-row>
          <b-row>
            <b-col sm="3">
              <label for="input-small">Vozidla:</label>
            </b-col>
            <b-col>
              <div
                v-for="(car, i) in currentDriver.vehicleInfo"
                :key="i"
                class="car h-10"
              >
                <b-card :title="getVehicleType(car.brandID)">
                  <b-card-text
                    >{{ car.modelYear }} <br />{{
                      getEngineType(car.engineType)
                    }}
                    <a class="text-danger" @click.prevent="deleteVeh(i)"
                      >Odstranit</a
                    >
                  </b-card-text>
                </b-card>
              </div>
              <a v-b-toggle.collapse-1>
                <div class="h-10">
                  <b-card>
                    <b-card-text>+ Přidat vůz</b-card-text>
                  </b-card>
                </div>
              </a>
              <b-collapse
                v-model="vehicleForm"
                id="collapse-1"
                style="position: absolute"
              >
                <b-card>
                  <b-card-body>
                    <b-form @submit.prevent="addVeh">
                      <b-row>
                        <b-col sm="4">
                          <label for="input-small">Značka vozidla</label>
                        </b-col>
                        <b-col sm="8">
                          <b-form-select
                            required
                            v-model="selectedBrand"
                            :options="vehicleBrands"
                            size="sm"
                            class="mt-1"
                          ></b-form-select>
                        </b-col>
                      </b-row>
                      <b-row>
                        <b-col sm="4">
                          <label for="input-small">Motorizace</label>
                        </b-col>
                        <b-col sm="8">
                          <b-form-select
                            required
                            v-model="selectedEngine"
                            :options="vehicleEngines"
                            size="sm"
                            class="mt-1"
                          ></b-form-select>
                        </b-col>
                      </b-row>
                      <b-row>
                        <b-col sm="4">
                          <label for="input-small">Rok výroby</label>
                        </b-col>
                        <b-col sm="8">
                          <b-form-input
                            required
                            type="number"
                            v-model="carYear"
                            placeholder="Zadejte rok výroby"
                          ></b-form-input>
                        </b-col>
                      </b-row>
                      <b-btn type="submit">Přidat</b-btn>
                    </b-form>
                  </b-card-body>
                </b-card>
              </b-collapse>
            </b-col>
          </b-row>
        </b-form>
      </div>
    </b-modal>
    <b-btn
      style="margin-left: -2px; margin-top: 25px"
      @click.prevent="postApi"
      v-bind:disabled="working ? true : false"
      >{{ working ? "Zpracovávám..." : "Zpracovat data" }}</b-btn
    >
    <div class="results" v-if="outputData">
      <h5>Statistika:</h5>
      <b-card-group deck>
        <b-card
          bg-variant="primary"
          text-variant="white"
          header="Průměrný věk"
          class="text-center"
        >
          <b-card-text>{{ outputData.averageAge }} let</b-card-text>
        </b-card>

        <b-card
          bg-variant="secondary"
          text-variant="white"
          header="Stejná příjmení"
          class="text-center"
          v-if="outputData.sameLastnames"
        >
          <b-card-text
            >{{
              Object.keys(outputData.sameLastnames).length
            }}
            případů</b-card-text
          >
        </b-card>

        <b-card
          bg-variant="success"
          text-variant="white"
          header="Nejoblíbenější značka vozidla"
          class="text-center"
        >
          <b-card-text>{{ getTopBrand() }}</b-card-text>
        </b-card>
      </b-card-group>
    </div>
  </div>
</template>

<script>
import config from "../config.js";
import Vue from "vue";
import axios from "axios";
import VueAxios from "vue-axios";

Vue.use(VueAxios, axios);

export default {
  name: "visualForm",
  data() {
    return {
      modalform: false,
      vehicleForm: false,
      selectedBrand: null,
      selectedEngine: null,
      carYear: 2020,
      url: config.apiUrl,
      outputData: "",
      working: false,
      // importedFile: null,
      eyeColors: [
        { value: null, text: "Zvolte barvu očí" },
        { value: "blue", text: "Modré" },
        { value: "green", text: "Zelené" },
        { value: "brown", text: "Hnědé" },
      ],
      vehicleBrands: [
        { value: null, text: "Vyberte značku vozu" },
        { value: 0, text: "BMW" },
        { value: 1, text: "Audi" },
        { value: 2, text: "Mercedes" },
        { value: 4, text: "Fiat" },
        { value: 5, text: "Renault" },
        { value: 6, text: "Lexus" },
        { value: 7, text: "Ferrari" },
        { value: 8, text: "Porsche" },
        { value: 9, text: "Kia" },
      ],
      vehicleEngines: [
        { value: null, text: "Vyberte typ motoru" },
        { value: "Electric", text: "Elektromotor" },
        { value: "Hybrid", text: "Hybridní motor" },
        { value: "Diesel", text: "Naftový motor" },
        { value: "Petrol", text: "Benzínový motor" },
      ],
      editing: false,
      editingID: null,
      editingIndex: null,
      birthDate: null,
      currentDriver: {
        id: 0,
        driver: {
          eyeColor: null,
        },
        vehicleInfo: [],
      },
      drivers: [],
    };
  },
  methods: {
    getVehicleType(vehTypeID) {
      let text = "";
      this.vehicleBrands.forEach((brand) => {
        if (brand.value === vehTypeID) text = brand.text;
      });
      return text;
    },
    getEngineType(engTypeID) {
      let text = "";
      this.vehicleEngines.forEach((engine) => {
        if (engine.value === engTypeID) text = engine.text;
      });
      return text;
    },
    addVeh() {
      let vehData = {
        brandID: this.selectedBrand,
        modelYear: this.carYear,
        engineType: this.selectedEngine,
      };
      this.currentDriver.vehicleInfo.push(vehData);
      this.selectedBrand = null;
      this.carYear = 2020;
      this.selectedEngine = null;
      this.vehicleForm = false;
    },
    deleteVeh(veh) {
      this.currentDriver.vehicleInfo.splice(veh);
    },
    getAge(date) {
      const birthdate = new Date(date);
      return new Date().getFullYear() - birthdate.getFullYear();
    },
    addDriver() {
      if (
        this.currentDriver.driver.firstName &&
        this.currentDriver.driver.lastName &&
        this.currentDriver.driver.eyeColor &&
        this.currentDriver.driver.firstName
      ) {
        this.drivers.push(this.currentDriver);
        this.modalform = false;
        this.clearForm();
      }
    },
    getBiggestID() {
      let ids = [];
      this.drivers.forEach((driver) => {
        ids.push(driver.id);
      });
      return Math.max.apply(null, ids);
    },
    clearForm() {
      this.currentDriver = {
        id: this.drivers.length === 0 ? 0 : this.getBiggestID() + 1,
        driver: {
          eyeColor: null,
        },
        vehicleInfo: [],
      };
    },
    startEditDriver(id) {
      this.currentDriver = this.drivers.find((driver) => driver.id == id);
      this.editingIndex = this.drivers.indexOf(this.currentDriver);
    },
    editDriver() {
      this.drivers[this.editingIndex] = this.currentDriver;
      this.clearForm();
      this.modalform = false;
    },
    removeDriver(id) {
      this.drivers.length === 1 ? (this.drivers = []) : null;
      const index = this.drivers.findIndex((driver) => driver.id === id);
      this.drivers.splice(index, 1);
      this.drivers.forEach((driver) => {
        if (driver.id > id) driver.id -= 1;
      });
    },
    postApi() {
      if (this.drivers.length > 0) {
        const inputData = JSON.stringify(this.drivers);
        this.working = true;
        axios
          .post(this.url, inputData, {
            headers: { "Content-Type": "application/json" },
          })
          .then((res) => {
            this.outputData = res.data.data;
          })
          .catch((err) => {
            this.outputData = err;
          })
          .finally(() => (this.working = false));
      }
    },
    formatNames(files) {
      return files.length === 1
        ? files[0].name
        : `${files.length} souborů vybráno`;
    },
    getTopBrand() {
      let result;
      const obj = this.outputData.mostPopularVehicleModels;
      let max = 0;
      for (const [key, value] of Object.entries(obj)) {
        if (value > max) {
          (max = value), (result = key);
        }
      }
      return result;
    },
    // importFile() {
    //   const file = this.importedFile;
    //   const fr = new FileReader();
    //   fr.readAsText(file);
    //   fr.onload = function () {
    //     const obj = JSON.parse(fr.result);
    //     this.drivers = obj;
    //     console.log(obj);
    //   };
    // },
  },
};
</script>

<style lang="scss" scoped>
.results {
  .card .card-body .card-text {
    color: white;
  }
}
</style>