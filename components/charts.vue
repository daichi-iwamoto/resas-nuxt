<script>
import { Line, mixins } from 'vue-chartjs'
const { reactiveProp } = mixins

export default {
  extends: Line,
  mixins: reactiveProp,
  props: {
    pdata: {
      type: Object,
      default: null
    },
    poption: {
      type: Object,
      default: null
    }
  },
  mounted () {
    this.renderChart(this.pdata, this.poption)
  }
}
</script>
