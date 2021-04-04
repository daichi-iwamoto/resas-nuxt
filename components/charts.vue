<script>
import { Line } from 'vue-chartjs'

import 'chartjs-plugin-colorschemes/src/plugins/plugin.colorschemes'
import { PiYG11 } from 'chartjs-plugin-colorschemes/src/colorschemes/colorschemes.brewer'

export default {
  extends: Line,
  props: {

  },

  data () {
    return {
      chartdata: {
        labels: ['January', 'February', 'February', 'February'],
        datasets: [
          {
            label: ['Data One'],
            data: [40, 30],
            fill: false
          },
          {
            label: ['Data One'],
            data: [43, 33],
            fill: false
          },
          {
            label: ['Data 2'],
            data: [20, 10],
            fill: false
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
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
        },
        plugins: {
          colorschemes: {
            scheme: PiYG11
          }
        }
      }
    }
  },
  mounted () {
    this.renderChart(this.chartdata, this.options)
  }
}
</script>
