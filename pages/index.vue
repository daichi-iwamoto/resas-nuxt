<template>
  <div class="container">
    <div>
      <h1 class="title">
        resas-nuxt
      </h1>
      <div class="links">
        <div v-for="pref in prefs" :key="pref.prefCode" class="link">
          <label>
            <input v-model="checkedPrefCode" v-bind:value="pref.prefCode" type="checkbox" @change="prefChecked()">
            {{ pref.prefName }}
          </label>
        </div>
      </div>
    </div>
    <Barchart />
  </div>
</template>

<script>
import Barchart from '@/components/charts.vue'

export default {
  components: {
    Barchart
  },
  async asyncData ({ $axios }) {
    const PrefsURL = '/resasApi/api/v1/prefectures'

    const prefData = await $axios.get(PrefsURL, {
      headers: { 'X-API-KEY': process.env.API_KEY }
    })

    return { prefs: prefData.data.result }
  },

  data () {
    return {
      checkedPrefCode: [],
      checkedPrefName: [],
      checkYear: [],
      populaData: [],
      xLabel: [],
      yLabel: []
    }
  },

  methods: {
    prefChecked () {
      const populaURL = '/resasApi/api/v1/population/composition/perYear'

      this.checkYear = []
      this.checkedPrefName = []
      this.populaData = []

      // 人口データ取得
      this.checkedPrefCode.map((pCode, index) => {
        const data = []
        const getPopulaData = this.$axios.$get(populaURL, {
          params: {
            prefCode: pCode,
            cityCode: '-'
          },
          headers: { 'X-API-KEY': process.env.API_KEY }
        }).then((res) => {
          res.result.data.map((datas) => {
            if (datas.label === '総人口') {
              // 該当都道府県情報
              this.prefs.map((pref) => {
                if (pref.prefCode === pCode) {
                  pName = pref.prefName
                }
                return pref
              })
              datas.data.map((populaNum) => {
                // 年度の取得
                if (index === 0) {
                  this.checkYear.push(populaNum.year)
                }
                this.checkYear.push(populaNum.value)
                return populaNum
              })
            }
            return datas
          })
        })
        return getPopulaData
      })
      console.log(this.checkYear)
    }
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family:
    'Quicksand',
    'Source Sans Pro',
    -apple-system,
    BlinkMacSystemFont,
    'Segoe UI',
    Roboto,
    'Helvetica Neue',
    Arial,
    sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}
</style>
