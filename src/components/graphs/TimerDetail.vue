<script>
import { Doughnut } from 'vue-chartjs'
export default {
  extends: Doughnut,
  props: {
    options: { type: Object, default: null },
    params: { type: Array, default: () => [] },
    todayValue: { type: String, default: '' },
  },
  mounted() {
    this.setChartConfig()
    this.renderChart(this.formatParams(this.params), this.options)
  },
  methods: {
    formatParams(params) {
      const labels = []
      const datasets = [{ data: [], backgroundColor: [] }]
      params.map((param) => {
        labels.push(param.label)
        datasets[0].data.push(param.value)
        datasets[0].backgroundColor.push(param.color)
      })
      datasets[0].borderColor = 'transparent'
      return { labels, datasets }
    },
    setChartConfig() {
      this.addPlugin({
        afterDraw: (chart, go) => {
          const ctx = chart.ctx
          chart.data.datasets.forEach((dataset, i) => {
            let dataSum = 0
            dataset.data.forEach((element) => {
              dataSum += element
            })
            const meta = chart.getDatasetMeta(i)
            if (!meta.hidden) {
              meta.data.forEach(function (element, index) {
                // フォントの設定
                const fontSize = 12
                const fontStyle = 'normal'
                const fontFamily = 'Helvetica Neue'
                ctx.fillStyle = '#000'
                ctx.font = Chart.helpers.fontString(
                  fontSize,
                  fontStyle,
                  fontFamily
                )
                // ラベルをパーセント表示に変更
                const labelString = chart.data.labels[index]
                const dataString =
                  Math.round((dataset.data[index] / dataSum) * 100).toString() +
                  '%'
                // positionの設定
                ctx.textAlign = 'center'
                ctx.textBaseline = 'middle'
                const padding = -2
                const position = element.tooltipPosition()
                // ツールチップに変更内容を表示
                ctx.fillText(
                  labelString,
                  position.x,
                  position.y - fontSize / 2 - padding
                ) // title
                ctx.fillText(
                  dataString,
                  position.x,
                  position.y + fontSize / 2 - padding
                ) // データの百分率
              })
            }
          })
          const fontSize = 24
          const fontStyle = 'normal'
          const fontFamily = 'Helvetica Neue'
          ctx.fillStyle = '#000'
          ctx.font = Chart.helpers.fontString(fontSize, fontStyle, fontFamily)
          ctx.textAlign = 'center'
          ctx.textBaseline = 'middle'
          ctx.fillText(this.todayValue, chart.width / 2, chart.height / 2)
        },
      })
    },
  },
}
</script>
