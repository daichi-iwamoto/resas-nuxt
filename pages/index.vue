<template>
  <div class="container">
    <header>
      <h1 class="title">resas-nuxt</h1>
    </header>
    <div class="links_container">
      <div v-for="pref in prefs" :key="pref.prefCode" class="link">
        <label>
          <input
            v-model="checkedPrefCode"
            :value="pref.prefCode"
            type="checkbox"
            @change="prefChecked()"
          />
          {{ pref.prefName }}
        </label>
      </div>
    </div>
    <div class="graph_container">
      <Chart v-if="flag" :chart-data="chartdata" :options="options" />
    </div>
  </div>
</template>

<script>
import Chart from "@/components/charts.vue"
import "chartjs-plugin-colorschemes/src/plugins/plugin.colorschemes"
import { PiYG11 } from "chartjs-plugin-colorschemes/src/colorschemes/colorschemes.brewer"
export default {
  components: {
    Chart,
  },
  async asyncData({ $axios }) {
    const PrefsURL = "/resasApi/api/v1/prefectures"
    const prefData = await $axios.get(PrefsURL, {
      headers: { "X-API-KEY": process.env.API_KEY },
    })
    return { prefs: prefData.data.result }
  },
  data() {
    return {
      flag: false,
      checkedPrefCode: [],
      populaData: [],
      chartdata: null,
      options: {
        responsive: true,
        scales: {
          xAxes: [
            {
              scaleLabel: {
                display: true,
                labelString: "年度",
              },
            },
          ],
          yAxes: [
            {
              scaleLabel: {
                display: true,
                labelString: "人口数",
              },
            },
          ],
        },
        plugins: {
          colorschemes: {
            scheme: PiYG11,
          },
        },
      },
    }
  },
  methods: {
    async prefChecked() {
      this.flag = false
      const populaURL = "/resasApi/api/v1/population/composition/perYear"
      // データの初期化
      this.populaData = []
      // 年度一時保存
      const labelYear = []
      // 都道府県数分取得
      for (let i = 0; i < this.checkedPrefCode.length; i++) {
        await this.$axios
          .get(populaURL, {
            params: {
              prefCode: this.checkedPrefCode[i],
              cityCode: "-",
            },
            headers: { "X-API-KEY": process.env.API_KEY },
          })
          .then((res) => {
            // 都道府県名一時保存
            let datasetLabel
            // 人口数一時保存
            const datasetData = []
            for (let j = 0; j < res.data.result.data.length; j++) {
              // 年度取得
              if (labelYear.length === 0) {
                for (let l = 0; l < res.data.result.data[j].data.length; l++) {
                  labelYear.push(res.data.result.data[j].data[l].year)
                }
              }
              // 都道府県名取得
              for (let x = 0; x < this.prefs.length; x++) {
                if (this.prefs[x].prefCode === this.checkedPrefCode[i]) {
                  datasetLabel = this.prefs[x].prefName
                }
              }
              // 総人口データ取得
              if (res.data.result.data[j].label === "総人口") {
                for (let k = 0; k < res.data.result.data[j].data.length; k++) {
                  datasetData.push(res.data.result.data[j].data[k].value)
                }
              }
            }
            // 同期確認
            // console.log(i + '回目！')
            this.populaData.push({
              label: datasetLabel,
              data: datasetData,
              fill: false,
            })
          })
      }
      this.chartdata = { labels: labelYear, datasets: this.populaData }
      this.flag = true
    },
  },
}
</script>
