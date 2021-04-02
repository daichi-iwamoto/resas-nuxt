<template>
  <div class="container">
    <div>
      <h1 class="title">
        resas-nuxt
      </h1>
      <div class="links">
        <div v-for="pref in prefs" :key="pref.prefCode" class="link">
          <input v-bind:id="'pref-' + pref.prefCode" type="checkbox">
          <label v-bind:for="'pref-' + pref.prefCode">
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

    const data = await $axios.get(PrefsURL, {
      headers: { 'X-API-KEY': process.env.API_KEY }
    })

    return { prefs: data.data.result }
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
