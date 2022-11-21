<template>
  <div class="wrapper">
    <div class="temp">
      <div class="temp__row">
        Подтверждено бронирований
        <input
          v-model="dataPieOne[0].value"
          width="30px"
          min="0"
          type="number"
          @input="createPie(dataPieOne, '#diagram')"
        />
      </div>
      <div class="temp__row">
        Поступило запросов
        <input
          v-model="dataPieOne[1].value"
          min="0"
          type="number"
          @input="createPie(dataPieOne, '#diagram')"
        />
      </div>
      <div class="temp__row">
        Завершено бронирований
        <input
          v-model="dataPieOne[2].value"
          min="0"
          type="number"
          @input="createPie(dataPieOne, '#diagram')"
        />
      </div>
      <div class="temp__row">
        Отменено бронирований
        <input
          v-model="dataPieOne[3].value"
          min="0"
          type="number"
          @input="createPie(dataPieOne, '#diagram')"
        />
      </div>
    </div>

    <div id="diagram" class="diagram-pie"></div>
    <hr />
    <div class="temp">
      <div class="temp__row">
        Подтверждено бронирований
        <input
          v-model="dataPieTwo[0].value"
          width="30px"
          min="0"
          type="number"
          @input="createPie(dataPieTwo, '#diagram-2')"
        />
      </div>
      <div class="temp__row">
        Поступило запросов
        <input
          v-model="dataPieTwo[1].value"
          min="0"
          type="number"
          @input="createPie(dataPieTwo, '#diagram-2')"
        />
      </div>
      <div class="temp__row">
        Завершено бронирований
        <input
          v-model="dataPieTwo[2].value"
          min="0"
          type="number"
          @input="createPie(dataPieTwo, '#diagram-2')"
        />
      </div>
      <div class="temp__row">
        Отменено бронирований
        <input
          v-model="dataPieTwo[3].value"
          min="0"
          type="number"
          @input="createPie(dataPieTwo, '#diagram-2')"
        />
      </div>
    </div>

    <div id="diagram-2" class="diagram-pie"></div>
  </div>
</template>

<script>
import * as d3 from 'd3'

export default {
  name: 'D3Js',
  data() {
    return {
      dataPieOne: [
        { id: 0, name: 'Подтверждено бронирований', key: 'approved', value: 1, color: '#9B0D89' },
        { id: 1, name: 'Поступило запросов', key: 'recieved', value: 5, color: '#164286' },
        { id: 2, name: 'Завершено бронирований', key: 'completed', value: 2, color: '#7517A9' },
        { id: 3, name: 'Отменено бронирований', key: 'canceled', value: 3, color: '#F79E1B' },
      ],
      dataPieTwo: [
        { id: 0, name: 'Подтверждено бронирований', key: 'approved', value: 24, color: '#9B0D89' },
        { id: 1, name: 'Поступило запросов', key: 'recieved', value: 25, color: '#164286' },
        { id: 2, name: 'Завершено бронирований', key: 'completed', value: 24, color: '#7517A9' },
        { id: 3, name: 'Отменено бронирований', key: 'canceled', value: 21, color: '#F79E1B' },
      ],
    }
  },
  mounted() {
    this.createPie(this.dataPieOne, '#diagram')
    this.createPie(this.dataPieTwo, '#diagram-2')
  },
  methods: {
    createPie(dataPie, anchor) {
      if (document.querySelector(anchor).firstChild) {
        document.querySelector(anchor).firstChild.remove()
      }
      let copiedDataPie = dataPie.slice()
      copiedDataPie = copiedDataPie.filter((item) => item.value > 0)
      if (dataPie.filter((item) => item.value > 19).length < dataPie.length) {
        // ? половинчатый график, если каждое значение меньше 20
        const sumDataPie = dataPie.reduce((acc, item) => acc + item.value, 0)
        const whiteSemiCircleValue = sumDataPie
        copiedDataPie.push({
          id: 4,
          name: 'whiteSemiCircle',
          key: 'whiteSemiCircle',
          value: whiteSemiCircleValue,
          color: '#F5F5F5',
        })
      }
      const viewWidth = 800
      const thickness = 0
      const viewHeight = 500
      const radius = viewWidth / 2
      const colorArray = copiedDataPie.map((d) => d.color)
      const svg = d3
        .select(anchor)
        .append('svg')
        .attr('viewBox', `0 0 ${viewWidth + thickness} ${viewHeight + thickness}`)
        .style('overflow', 'visible')
        .attr('class', 'pie')
      //  .attr(
      //    'transform',
      //    `translate( ${viewWidth / 3 + thickness / 2}, ${viewHeight / 3 + thickness / 2})`
      //  )
      const formattedData = d3
        .pie()
        .padAngle(0.0174533)
        .sortValues((a, b) => a - b)
        .value((d) => d.value)(copiedDataPie)

      const arcGenerator = d3
        .arc()
        .innerRadius(90)
        .outerRadius(function (d, i) {
          if (d.data.name === 'whiteSemiCircle') return 180
          return Math.log(Math.sqrt(d.value) + 0.5) * 40 + 180
        })

      const color = d3.scaleOrdinal().range(colorArray)

      svg
        .selectAll()
        .data(formattedData)
        .join('path')
        .attr('d', arcGenerator)
        .attr('fill', (d) => color(d.data.name))
        // .style('opacity', 0.7)
        .attr(
          'transform',
          `translate( ${viewWidth / 3 + thickness / 2}, ${viewHeight / 3 + thickness / 2})`
        )
      svg
        .selectAll()
        .data(formattedData)
        .join('text')
        .text((d) => d.data.value)
        .attr('class', function (d) {
          if (d.data.name === 'whiteSemiCircle') {
            return 'diagram-pie__invisible-part'
          }
          return 'diagram-pie__text-arc'
        })
        .style('font-size', function (d) {
          return Math.log(d.data.value + 5) * 20 + 'px'
        })

        // .style('font-size', (d) => Math.log(d.data.value + 5) * 20 + 'px')
        //
        .attr(
          'transform',
          (d) =>
            `translate(${viewWidth / 3 + thickness / 2 + arcGenerator.centroid(d)[0] * 0.9},${
              viewHeight / 3 + thickness / 2 + arcGenerator.centroid(d)[1] * 0.9
            })`
        )
        .style('text-anchor', 'middle')
      //  .attr(
      //    'transform',
      //    `translate( ${viewWidth / 3 + thickness / 2}, ${viewHeight / 3 + thickness / 2})`
      //  )

      svg
        .append('text')
        .text('За последние')
        .attr('class', 'diagram-pie__text-middle')
        .style('text-anchor', 'middle')
        .attr(
          'transform',
          `translate( ${viewWidth / 3 + thickness / 2}, ${viewHeight / 3 + thickness / 2})`
        )
      svg
        .append('text')
        .text(` 30 дней`)
        .attr('class', 'diagram-pie__text-middle')
        .style('text-anchor', 'middle')
        .attr('dy', '1.5rem')
        .attr(
          'transform',
          `translate( ${viewWidth / 3 + thickness / 2}, ${viewHeight / 3 + thickness / 2})`
        )
      const legendRectSize = 17
      const legendSpacing = 26

      const legend = svg
        .selectAll('.legend')
        .data(color.domain())
        .enter()
        .append('g')
        .attr('class', 'legend')
        .attr('transform', function (legendData, i) {
          const itemHeight = legendRectSize + legendSpacing
          const offset = legendRectSize * color.domain().length
          const horz = viewWidth / 3 + thickness / 2 + viewWidth / 2
          const vert = viewHeight / 3 + thickness / 2 + i * itemHeight + legendRectSize - offset
          return `translate(${horz}, ${vert})`
        })
      legend
        .append('rect')
        .attr('rx', 5)
        .attr('x', 0)
        .attr('y', 0)
        .attr('ry', 5)
        .attr('width', legendRectSize)
        .attr('height', legendRectSize)
        .attr('class', function (legendData) {
          if (legendData === 'whiteSemiCircle') {
            return 'diagram-pie__invisible-part'
          }
        })
        .style('fill', color)

      legend
        .append('text')
        .attr('x', 10 + legendRectSize)
        // .attr('y', legendRectSize / 2 + 3)
        .attr('dy', '0.9rem')
        .attr('class', function (legendData) {
          if (legendData === 'whiteSemiCircle') {
            return 'diagram-pie__invisible-part'
          }
          return 'diagram-pie__legend-text'
        })
        .text((legendData) => legendData)
    },
  },
}
</script>
 
<style lang="scss"  >
.temp {
  margin: 50px auto 20px auto;

  width: 350px;
  &__row {
    display: flex;
    justify-content: space-between;
    input {
      width: 45px;
    }
  }
}
.diagram-pie {
  margin: 0px 0px 220px 220px;
  max-width: 800px;
  max-height: 500px;
  &__invisible-part {
    display: none;
  }
  &__text-middle {
    fill: #676767;
    font-size: 20px;
    line-height: 110%;
  }
  &__text-arc {
    fill: #ffffff;
  }
  &__legend-text {
    font-size: 16px;
    line-height: 131%;
  }
}
</style>