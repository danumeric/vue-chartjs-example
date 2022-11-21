<template>
  <div class="test">
    <div id="legend-container"></div>
    <div class="wrapper-chart">
      <div class="circle-white"></div>
      <canvas id="DoughnutChart"> </canvas>
    </div>
  </div>
</template>

<script>
import { Chart, registerables } from 'chart.js'

Chart.register(...registerables)

export default {
  name: 'ChartJs',

  mounted() {
    const getOrCreateLegendList = (chart, id) => {
      const legendContainer = document.getElementById(id)
      let listContainer = legendContainer.querySelector('ul')

      if (!listContainer) {
        listContainer = document.createElement('ul')
        listContainer.style.display = 'flex'
        listContainer.style.flexDirection = 'row'
        listContainer.style.margin = 0
        listContainer.style.padding = 0

        legendContainer.appendChild(listContainer)
      }

      return listContainer
    }

    const htmlLegendPlugin = {
      id: 'htmlLegend',
      afterUpdate(chart, args, options) {
        const ul = getOrCreateLegendList(chart, options.containerID)

        // Remove old legend items
        while (ul.firstChild) {
          ul.firstChild.remove()
        }

        // Reuse the built-in legendItems generator
        const items = chart.options.plugins.legend.labels.generateLabels(chart)

        items.forEach((item) => {
          const li = document.createElement('li')
          li.style.alignItems = 'center'
          li.style.cursor = 'pointer'
          li.style.display = 'flex'
          li.style.flexDirection = 'row'
          li.style.marginLeft = '10px'

          li.onclick = () => {
            const { type } = chart.config
            if (type === 'pie' || type === 'doughnut') {
              // Pie and doughnut charts only have a single dataset and visibility is per item
              chart.toggleDataVisibility(item.index)
            } else {
              chart.setDatasetVisibility(
                item.datasetIndex,
                !chart.isDatasetVisible(item.datasetIndex)
              )
            }
            chart.update()
          }

          // Color box
          const boxSpan = document.createElement('span')
          boxSpan.style.background = item.fillStyle
          boxSpan.style.borderColor = item.strokeStyle
          boxSpan.style.borderWidth = item.lineWidth + 'px'
          boxSpan.style.display = 'inline-block'
          boxSpan.style.height = '20px'
          boxSpan.style.marginRight = '10px'
          boxSpan.style.width = '20px'

          // Text
          const textContainer = document.createElement('p')
          textContainer.style.color = item.fontColor
          textContainer.style.margin = 0
          textContainer.style.padding = 0
          textContainer.style.textDecoration = item.hidden ? 'line-through' : ''

          const text = document.createTextNode(item.text)
          textContainer.appendChild(text)

          li.appendChild(boxSpan)
          li.appendChild(textContainer)
          ul.appendChild(li)
        })
      },
    }
    const chart = new Chart(document.getElementById('DoughnutChart'), {
      type: 'polarArea',
      data: {
        labels: ['4', '4', '1', '5', '1'],
        datasets: [
          {
            label: 'Population (millions)',
            backgroundColor: ['#3e95cd', '#8e5ea2', '#3cba9f', '#e8c3b9', '#c45850'],
            data: [
              Math.log(4 + 10),
              Math.log(4 + 10),
              Math.log(1 + 10),
              Math.log(5 + 10),
              Math.log(1 + 10),
            ],
            borderWidth: '7',
            radius: '80%',
            // borderAlign: ['inner', 'inner', 'inner', 'inner', 'inner'],
          },
        ],
      },
      options: {
        cutout: '30%',
        scales: {
          r: {
            grid: {
              display: false,
            },
            ticks: {
              display: false,
            },
          },
        },
        responsive: true,

        plugins: {
          htmlLegend: {
            // ID of the container to put the legend in
            containerID: 'legend-container',
          },
          legend: {
            display: false,
          },
          title: {
            display: false,
            text: 'Chart.js Doughnut Chart',
          },
        },
      },
      plugins: [htmlLegendPlugin],
    })

    // const ctx = document.getElementById('planet-chart')
    // const myChart = new Chart(ctx, this.planetChartData)
  },
}
</script>
<style lang="scss" scoped>
.wrapper-chart {
  position: relative;
}
.test {
  margin: 100px 0px 0px 500px;

  max-width: 700px;
}
.circle-white {
  position: absolute;
  top: 50%;
  left: 50%;
  -webkit-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
  z-index: 0;
  border-radius: 50%;
  background: #ffffff;
  width: 30%;
  height: 30%;
}

#legend ul {
  font: 12px Verdana;
  list-style: none;
  white-space: nowrap;
}

#legend li span {
  display: inline-block;
  vertical-align: -9.4px;
  margin: 0 5px 8px 0;
  width: 36px;
  height: 12px;
}
</style>