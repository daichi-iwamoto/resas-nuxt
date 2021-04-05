<script>
import { Line } from 'vue-chartjs'

// import 'chartjs-plugin-colorschemes/src/plugins/plugin.colorschemes'
// import { PiYG11 } from 'chartjs-plugin-colorschemes/src/colorschemes/colorschemes.brewer'

// import { delay } from 'chartjs-plugin-deferred/dist/chartjs-plugin-deferred'

// const { reactiveProp } = mixins

export default {
  extends: Line,
  props: {
    chartData: {
      type: Object,
      default: null
    },
    options: {
      type: Object,
      default: null
    }
  },
  mounted () {
    this.renderChart(this.chartData, this.options)
  }
}
</script>
