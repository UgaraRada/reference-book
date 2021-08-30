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
      v-for="(it, key) in objectsByRegionCode"
      :key="key"
      class="pa-4 mt-4"
    >
      <v-list-item-content class="card">
        <v-list-item-title class="card__title">
          {{ key }}
        </v-list-item-title>
        <v-list-item-subtitle class="card__text">
          <span>Количество активных объектов: {{ it.active }}</span>
          <span>Количество отключенных объектов: {{ it.disabled }}</span>
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
      branch: {},
      region: {},
      branchesAndRegions: [],
      objectsByRegionCode: {},
    };
  },

  created() {
    this.getBranchesAndRegions();
  },

  methods: {
    showVendors(code) {
      this.objectsByRegionCode = this.getObjectsByRegionCode(code)[code].reduce((acc, it) => {
        if (!acc[it.vendor]) {
          acc[it.vendor] = {
            disabled: 0,
            active: 0,
          };
        }

        if (it.status === 'DISABLE') {
          acc[it.vendor].disabled += 1;
        }

        if (it.status === 'ACTIVE') {
          acc[it.vendor].active += 1;
        }

        return acc;
      }, {});
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
