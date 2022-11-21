<template>
  <!-- https://codepen.io/JFlo/pen/QOMvOp -->
  <div id="diagram" class="diagram"></div>
</template>

<script>
import * as d3 from 'd3'

export default {
  name: 'D3Js',
  data() {
    return {
      pieData: [
        { name: 'Подтверждено бронирований', value: 1, color: '#F79E1B' },
        { name: 'Поступило запросов', value: 4, color: '#9B0D89' },
        { name: 'Завершено бронирований', value: 4, color: '#7517A9' },
        { name: 'Отменено бронирований', value: 5, color: '#164286' },
        { name: 'Unknown', value: 19, color: '#F5F5F5' },
      ],
    }
  },
  mounted() {
    bakeDonut(this.pieData)

    function bakeDonut(d) {
      // let activeSegment
      const data = d // .sort((a, b) => b.value - a.value)
      const viewWidth = 600
      const viewHeight = 500
      const svgWidth = viewHeight
      const svgHeight = viewHeight
      const thickness = 40
      const colorArray = data.map((k) => k.color)
      const el = d3.select('#diagram')
      // const radius = Math.min(svgWidth, svgHeight) / 2
      const color = d3.scaleOrdinal().range(colorArray)

      // const max = d3.max(data, (maxData) => maxData.value)

      const svg = el
        .append('svg')
        .attr('viewBox', `0 0 ${viewWidth + thickness} ${viewHeight + thickness}`)
        .attr('class', 'pie')
      //     .attr('width', viewWidth)
      //     .attr('height', svgHeight)

      const g = svg
        .append('g')
        .attr(
          'transform',
          `translate( ${svgWidth / 2 + thickness / 2}, ${svgHeight / 2 + thickness / 2})`
        )
      //   g.append('foreignObject')
      //     .attr('width', '140px')
      //     .attr('x', '-10px')
      //     .attr('height', '50px')
      //     .append('xhtml:div')
      //     .style('color', '#000')
      //     .style('text-align', 'center')
      //     .style('width', '100%')
      //     .style('height', '100%')
      //     .style('padding', '5px')
      //     .style('font-size', `10px`)
      //     .style('overflow-y', 'auto')
      //     .html('The text to display')

      g.append('text')
        .text(`За последние`)

        .attr('class', 'diagram__text-middle')
        .attr('text-anchor', 'middle')
        .attr('dy', '1rem')
      g.append('text')
        .text(` 30 дней`)

        .attr('class', 'diagram__text-middle')
        .attr('text-anchor', 'middle')
        .attr('dy', '2.1rem')
      //!
      // .outerRadius(radius)

      //  const arcHover = d3
      //    .arc()
      //    .innerRadius(radius - (thickness + 5))
      //    .outerRadius(radius + 8)

      const pie = d3
        .pie()
        .value(function (pieData) {
          return pieData.value
        })
        .sort(null)

      const arc = d3.arc().innerRadius(100)

      const path = g
        .selectAll('path')
        .attr('class', 'data-path')
        .data(pie(data))
        .enter()
        .append('g')
        .attr('class', 'data-group')
        .each(function (pathData, i) {
          const group = d3.select(this)

          //   group
          //     .append('text')
          //     .text(`${pathData.data.value}`)
          //     .attr('class', 'data-text data-text__value')
          //     .attr('text-anchor', 'middle')
          //     .attr('dy', '1rem')
          //
          //   group
          //     .append('text')
          //     .text(`${pathData.data.name}`)
          //     .attr('class', 'data-text data-text__name')
          //     .attr('text-anchor', 'middle')
          //     .attr('dy', '3.5rem')

          // Set default active segment
          //  if (pathData.value === max) {
          //    const textVal = d3
          //      .select(this)
          //      .select('.data-text__value')
          //      .classed('data-text--show', true)

          //    const textName = d3
          //      .select(this)
          //      .select('.data-text__name')
          //      .classed('data-text--show', true)
          //  }
        })
        .append('path')
        .attr('d', arc)
        .attr(
          'd',
          arc.outerRadius(function (d, i) {
            return Math.log(d.value + 1) * 20 + 120
          })
        )

        .attr('fill', (fillData, i) => color(fillData.data.name))
        .attr('class', 'data-path')
      //  .on('mouseover', function () {
      //    const _thisPath = this
      //    const parentNode = _thisPath.parentNode

      //    if (_thisPath !== activeSegment) {
      //      activeSegment = _thisPath

      //      const dataTexts = d3.selectAll('.data-text').classed('data-text--show', false)

      //      const paths = d3.selectAll('.data-path').transition().duration(250).attr('d', arc)

      //      //   d3.select(_thisPath).transition().duration(250).attr('d', arcHover)

      //      const thisDataValue = d3
      //        .select(parentNode)
      //        .select('.data-text__value')
      //        .classed('data-text--show', true)
      //      const thisDataText = d3
      //        .select(parentNode)
      //        .select('.data-text__name')
      //        .classed('data-text--show', true)
      //    }
      //  })
      //  .each(function (v, i) {
      //    if (v.value === max) {
      //      const maxArc = d3.select(this).attr('d', arcHover)
      //      activeSegment = this
      //    }
      //    this._current = i
      //  })
 const legendRectSize = 15
      const legendSpacing = 10
      svg
        .append('text')

        .attr('x', legendRectSize + legendSpacing)
        .attr('y', legendRectSize - legendSpacing)
        .attr('class', 'legend-text')
        .text('11231231')

     

      const legend = svg
        .selectAll('.legend')
        .data(color.domain())
        .enter()
        .append('g')
        .attr('class', 'legend')
        .attr('transform', function (legendData, i) {
          const itemHeight = legendRectSize + legendSpacing
          const offset = legendRectSize * color.domain().length
          const horz = svgWidth + 80
          const vert = i * itemHeight + legendRectSize + (svgHeight - offset) / 2
          return `translate(${horz}, ${vert})`
        })

      legend
        .append('circle')
        .attr('r', legendRectSize / 2)
        .style('fill', color)

      legend
        .append('text')
        .attr('x', 0)
        .attr('y', 0)
        .attr('x', legendRectSize + legendSpacing)
        .attr('y', legendRectSize - legendSpacing)
        .attr('class', 'legend-text')
        .text((legendData) => legendData)
    }
  },
}
</script>

<style lang="scss"  scoped>
.diagram {
  width: 500px;
  height: 500px;
  &__text-middle {
    fill: #676767;
    display: block;
    transform: translateY(-0.5rem);
    font-size: 20px;
    line-height: 110%;
    text-align: center;
  }
}

.pie {
  margin: 50px 0px 0px 100px;
  font-family: 'Roboto';
  // position: absolute;
  // top: 50%;
  // left: 50%;
  // transform: translate(-50%, -50%);
  //  color: black;
  // filter: drop-shadow(0 2px 0px #333);
}

.data-path:hover {
  cursor: pointer;
}

.data-text {
  color: red;
  // fill: #fff;
  // transition: transform 0.2s ease-in-out;
}
.data-text__value {
  //  opacity: 0;font-weight: 300;
}
.data-text__name {
  transform: translateY(0.5rem);
  //  opacity: 0;
  font-size: 2rem;
}
.data-text--show {
  transform: translateY(0);
  -webkit-animation: fadeGraphTextIn 0.5s forwards;
  animation: fadeGraphTextIn 0.5s forwards;
}
.data-text:hover {
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.legend-text {
  // fill: #fff;
}

@-webkit-keyframes fadeGraphTextIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes fadeGraphTextIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>