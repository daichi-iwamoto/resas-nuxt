<template>
  <div class="container">
    <div>
      <h1 class="title">
        resas-nuxt
      </h1>
      <div class="links">
        <div v-for="pref in prefs" :key="pref.prefCode" class="link">
          <label>
            <input v-model="checkedPrefCode" :value="pref.prefCode" type="checkbox" @change="prefChecked()">
            {{ pref.prefName }}
          </label>
        </div>
      </div>
    </div>
    <Chart :chart-data="chartdata" :options="options" />
  </div>
</template>

<script>
import Chart from '@/components/charts.vue'

export default {
  components: {
    Chart
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
      checkYear: [],
      populaData: [],
      chartdata: null,
      options: {
        responsive: true,
        scales: {
          xAxes: [
            {
              scaleLabel: {
                display: true,
                labelString: '年度'
              }
            }
          ],
          yAxes: [
            {
              scaleLabel: {
                display: true,
                labelString: '人口数'
              }
            }
          ]
        }
      }
    }
  },

  methods: {
    prefChecked () {
      const populaURL = '/resasApi/api/v1/population/composition/perYear'

      this.checkYear = []
      this.populaData = []

      // 年度一時保存
      const labelYear = []

      // 人口データ取得
      this.checkedPrefCode.forEach((pCode, index) => {
        this.$axios.$get(populaURL, {
          params: {
            prefCode: pCode,
            cityCode: '-'
          },
          headers: { 'X-API-KEY': process.env.API_KEY }
        }).then((res) => {
          res.result.data.forEach((datas) => {
            // 都道府県名一時保存
            let datasetLabel
            // 人口数一時保存
            const datasetData = []
            // 総人口情報のみ取得
            if (datas.label === '総人口') {
              // 都道府県名取得
              this.prefs.forEach((pref) => {
                if (pref.prefCode === pCode) {
                  datasetLabel = pref.prefName
                  // データ取得
                  datas.data.forEach((populaNum) => {
                    datasetData.push(populaNum.value)
                    // 年度の取得
                    if (index === 0) {
                      labelYear.push(populaNum.year)
                    }
                  })
                }
              })
              this.populaData.push({ label: datasetLabel, data: datasetData, fill: false })
            }
          })
        })
      })
      this.chartdata = { labels: labelYear, datasets: this.populaData }
      console.log(this.chartdata)
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
