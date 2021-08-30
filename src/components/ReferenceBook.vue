<template>
  <v-container>
    <div>
      <select v-model="filial">
        <option
          disabled
          value=""
        >
          Филиал
        </option>
        <option
          v-for="(it, index) in dataList"
          :key="index"
          :value="it.branchCode"
        >
          {{ it.branchName }}
        </option>
      </select>
      {{ filial }}
    </div>
    <div>
      <select v-model="regCode">
        <option
          disabled
          value=""
        >
          Регион
        </option>
        <option
          v-for="(it, index) in getRegions(filial)"
          :key="index"
          :value="it.code"
        >
          {{ it.name }}
        </option>
        {{ regCode }}
      </select>
    </div>
    <!-- {{ dataList }} -->
    {{ getRegions(filial) }}
  </v-container>
</template>

<script>
export default {

  name: 'ReferenceBook',

  data() {
    return {
      filial: '',
      regCode: '',
      dataList: [
        this.getBranchesAndRegions().CN,
        this.getBranchesAndRegions().KV],
    };
  },

  methods: {
    getBranchesAndRegions() {
      return {
        CN: {
          branchCode: 'CN', // код филиала
          branchName: 'Центральный филиал', // Имя филиала
          regions: [ // список регионов внутри филиала
            { name: 'Брянская область', code: 32 }, // name: имя региона, code: код региона
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
    },
    getRegions(f) {
      return this.dataList.filter((it) => (it.branchCode === f ? it.regions : ''));
    },
  },
};
</script>

<style scoped lang="scss">

</style>
