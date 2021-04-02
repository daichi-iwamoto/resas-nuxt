<template>
  <div class="container">
    <div>
      <h1 class="title">
        resas-nuxt
      </h1>
      <div class="links">
        <div v-for="pref in prefs" :key="pref.prefCode" class="link">
          <label>
            <input v-model="checkedPref" v-bind:value="pref.prefCode" type="checkbox" @change="prefChecked()">
            {{ pref.prefName }}
          </label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  async asyncData ({ $axios }) {
    const PrefsURL = '/resasApi/api/v1/prefectures'

    const prefData = await $axios.get(PrefsURL, {
      headers: { 'X-API-KEY': process.env.API_KEY }
    })

    return { prefs: prefData.data.result }
  },

  data () {
    return {
      checkedPref: [],
      populaData: []
    }
  },

  methods: {
    prefChecked () {
      const populaURL = '/resasApi/api/v1/population/composition/perYear'

      this.populaData = []

      // 人口データ取得
      this.checkedPref.map((value) => {
        const getPopulaData = this.$axios.$get(populaURL, {
          params: {
            prefCode: value,
            cityCode: '-'
          },
          headers: { 'X-API-KEY': process.env.API_KEY }
        }).then((res) => {
          // 総人口のみ取得
          res.result.data.map((datas) => {
            if (datas.label === '総人口') {
              console.log(datas.data)
              this.populaData.push(datas.data)
            }
            return datas
          })
        })
        return getPopulaData
      })

      console.log(this.populaData)
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
