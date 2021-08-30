<template>
  <v-container>
    <div class="header">
      <v-select
        v-model="branch"
        class="header__elem"
        :items="branchesAndRegions"
        item-text="branchName"
        return-object
        label="Филиал"
        @change="region = {}"
      />
      <v-select
        v-model="region"
        class="header__elem"
        :items="branch.regions || []"
        :disabled="!branch.regions"
        item-text="name"
        return-object
        label="Регион"
      />
      <v-btn
        class="header__elem"
        elevation="4"
        :disabled="!region.code"
        color="#BBDEFB"
        @click="showVendors(region.code)"
      >
        Показать
      </v-btn>
    </div>
    <v-card
      v-for="(it, index) in vendorsName"
      :key="index"
      class="pa-4 mt-4"
    >
      <v-list-item-content class="card">
        <v-list-item-title class="card__title">
          {{ it.name }}
        </v-list-item-title>
        <v-list-item-subtitle class="card__text">
          <span>Количество активных объектов: {{ it.active }}</span>
          <span>Количество отключенных объектов: {{ it.disable }}</span>
        </v-list-item-subtitle>
      </v-list-item-content>
    </v-card>
  </v-container>
</template>

<script>
export default {

  name: 'ReferenceBook',

  data() {
    return {
      name: '',
      branch: {},
      region: {},
      vendors: {},
      vendorsName: [],
      setVendors: new Set(),
      branchesAndRegions: [],
    };
  },

  created() {
    this.getBranchesAndRegions();
  },

  methods: {
    showVendors(num) {
      this.vendorsName = [];
      const vendorsSet = new Set();
      this.vendors = this.getObjectsByRegionCode(num)[num];
      this.vendors.forEach((it) => vendorsSet.add(it.vendor));
      vendorsSet.forEach((it) => this.vendorsName.push({ name: it }));
      this.statusRecord();
    },
    statusRecord() {
      this.vendorsName.forEach((it) => { it.active = this.numActive(it.name); });
      this.vendorsName.forEach((it) => { it.disable = this.numDisable(it.name); });
    },
    numActive(name) {
      return this.vendors.filter((it) => it.vendor === name)
        .filter((it) => it.status === 'ACTIVE').length;
    },
    numDisable(name) {
      return this.vendors.filter((it) => it.vendor === name)
        .filter((it) => it.status === 'DISABLE').length;
    },
    getBranchesAndRegions() {
      const data = {
        CN: {
          branchCode: 'CN',
          branchName: 'Центральный филиал',
          regions: [
            { name: 'Брянская область', code: 32 },
            { name: 'Владимирская область', code: 33 },
            { name: 'Калужская область', code: 40 },
            { name: 'Курская область', code: 46 },
          ],
        },
        KV: {
          branchCode: 'KV',
          branchName: 'Кавказский филиал',
          regions: [
            { name: 'Республика Адыгея', code: 1 },
            { name: 'Республика Дагестан', code: 5 },
            { name: 'Краснодарский край', code: 23 },
            { name: 'Воронежская область', code: 36 },
          ],
        },
      };

      this.branchesAndRegions = Object.values(data);
    },
    getObjectsByRegionCode(regionCode) {
      const vendorList = ['HUAWEI', 'ZTE', 'NOKIA'];
      const statusList = ['ACTIVE', 'DISABLE'];
      const response = {};
      const gri = (max) => Math.floor(Math.random() * Math.floor(max));
      response[regionCode] = [];
      /* eslint no-plusplus: ["error", { "allowForLoopAfterthoughts": true }] */
      for (let i = 0; i < 100; i++) {
        response[regionCode].push({ vendor: vendorList[gri(3)], name: `CELL_${gri(999) + 1000}_${gri(9)}`, status: statusList[gri(2)] });
      }

      return response;
    },
  },
};
</script>

<style scoped lang="scss">
.header {
  display: flex;
  justify-content: space-around;
  align-items: baseline;

  &__elem {
    margin: 20px;
    width: 200px;
  }
}

.card {
  color: rgb(50, 110, 162);

  &__title {
    font-weight: bold;
  }

  &__text {
    display: flex;
    font-size: 1rem;
  }

  &__text span {
    line-height: 40px;
    margin-right: 30px;
  }
}

</style>
