<template>
  <g>
    <path
      class="area area--landuse"
      :d="d"
    />
  </g>
</template>

<script>
import { mapGetters } from 'vuex'
import { area } from 'd3-shape'
import { timeParse } from 'd3-time-format'
import { get, find, map } from 'lodash'

export default {
  props: ['el', 'scaleX', 'scaleY'],
  computed: {
    ...mapGetters([
      'currentPaths'
    ]),
    data () {
      const top = get(find(this.currentPaths, { scenario: 'historic-landuse-addition' }), 'values', [])
      const bottom = get(find(this.currentPaths, { scenario: 'historic' }), 'values', [])
      if (top.length && bottom.length) {
        return map(top, (t, i) => {
          return [timeParse('%Y')(t[0]), t[1], bottom[i][1]]
        })
      } else {
        return []
      }
    },
    d () {
      return this.drawArea()(this.data)
    }
  },
  methods: {
    drawArea () {
      const { scaleX, scaleY } = this
      return area()
        .x((d) => {
          return scaleX(d[0])
        })
        .y1((d) => {
          return scaleY(d[1])
        })
        .y0((d) => {
          return scaleY(d[2])
        })
    }
  }
}
</script>
