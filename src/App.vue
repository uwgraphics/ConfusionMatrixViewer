<template>
  <div id="app">
	<data-select/>
  </div>
</template>

<script>
import Vue from 'vue'

import ShapeShifter from './components/ShapeShifter'
import BaseMatrix from './components/BaseMatrix'
import Bipartite from './components/Bipartite'
import ColorLegend from './components/ColorLegend'
import DataSelect from './components/DataSelect'
import Marimekko from './components/Marimekko'
import MarkerMatrix from './components/MarkerMatrix'
import MarkerMatrixCell from './components/MarkerMatrixCell'
import Matrix from './components/Matrix'
import Pie from './components/Pie'
import Venn from './components/Venn'
import Views from './components/Views'
import TextBall from './components/TextBall'
import TextBox from './components/TextBox'

Vue.component("shape-shifter", ShapeShifter)
Vue.component("base-matrix", BaseMatrix)
Vue.component("bipartite", Bipartite)
Vue.component("color-legend", ColorLegend)
Vue.component("data-select", DataSelect)
Vue.component("matrix", Matrix)
Vue.component("marimekko", Marimekko)
Vue.component("marker-matrix", MarkerMatrix)
Vue.component("marker-matrix-cell", MarkerMatrixCell)
Vue.component("pie", Pie)
Vue.component("venn", Venn)
Vue.component("views", Views)
Vue.component("text-ball", TextBall)
Vue.component("text-box", TextBox)

Vue.mixin({
	methods: {
		transpose: function(a) {
			return a[0].map((_, c) => a.map(r => r[c]))
		},
	},
})

export default {
	name: 'app',
}
</script>
