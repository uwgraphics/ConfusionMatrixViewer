<template>
	<div class="Pie">
		<svg viewBox="0 0 200 200">
			<g :transform="`translate(${width/2}, ${height/2})`">
				<!-- Row Selected View -->
				<template v-if="rowSelected">
					<!-- Outer Pie Chart --> 
					<g v-for="(r, i) in rows">
						<path v-for="(c, j) in cols" @click="$emit('select', [i, j])"
							:d=rowOuterWedges[i][j]
							:fill=c.color
							:opacity=1-dim>
						</path>
					</g>
					<!-- Inner Pie Chart --> 
					<path v-for="(r, i) in rows" @click="$emit('select', [i, -1])"
						:d=rowInnerWedges[i]
						:fill=r.color
						:opacity=1-dim>
					</path>
					<!-- Draw Selected Darker -->
					<path v-for="(c, j) in cols"
						pointer-events="none"
						stroke="black"
						:d=rowOuterWedges[row][j]
						:fill=c.color>
					</path>
					<path
						pointer-events="none"
						stroke="black"
						:d=rowInnerWedges[row]
						:fill=rows[row].color>
					</path>
					<!-- Thicker Stroke around Selected Cell -->
					<path
						pointer-events="none"
						stroke="black"
						stroke-width=2
						:d=rowOuterWedges[row][col]
						:fill=cols[col].color>
					</path>
				</template>
				<!-- Column Selected View -->
				<template v-else>
					<!-- Outer Pie Chart --> 
					<g v-for="(c, j) in cols">
						<path v-for="(r, i) in rows" @click="$emit('select', [i, j])"
							:d=colOuterWedges[i][j]
							:fill=r.color
							:opacity=1-dim>
						</path>
					</g>
					<!-- Inner Pie Chart --> 
					<path v-for="(c, j) in cols" @click="$emit('select', [-1, j])"
						:d=colInnerWedges[j]
						:fill=c.color
						:opacity=1-dim>
					</path>
					<!-- Draw Selected Darker -->
					<path v-for="(r, i) in rows"
						pointer-events="none"
						stroke="black"
						:d=colOuterWedges[i][col]
						:fill=r.color>
					</path>
					<path
						pointer-events="none"
						stroke="black"
						:d=colInnerWedges[col]
						:fill=cols[col].color>
					</path>
					<!-- Thicker Stroke around Selected Cell -->
					<path
						pointer-events="none"
						stroke="black"
						stroke-width=2
						:d=colOuterWedges[row][col]
						:fill=rows[row].color>
					</path>
				</template>
			</g>
		</svg>
	</div>
</template>

<script>
import * as d3 from 'd3'

export default {
	name: 'Pie',
	props: ['matrix', 'rows', 'cols', 'row', 'col', 'rowSelected'],
	data: function() {
		const width = 200
		const height = 200
		return {
			width,
			height,
			outerRadius: Math.min(width, height)/2-2,
			innerRadius: Math.min(width, height)*3/10,
			dim: 0.5,
		}
	},
	computed: {
		rowSums: function() {
			return this.matrix.map(x => x.reduce((a, b) => a + b))
		},
		colSums: function() {
			return this.transpose(this.matrix).map(x => x.reduce((a, b) => a + b))
		},
		total: function() {
			return this.rowSums.reduce((a, b) => a + b)
		},
		rowInnerWedges: function() {
			const arcGen = d3.arc()
				.innerRadius(0)
				.outerRadius(this.innerRadius)
			const arcs = []
			let angle = 0
			for(let i = 0; i < this.rows.length; i += 1) {
				const nextAngle = angle + 2*Math.PI*(this.rowSums[i]/this.total)
				arcs.push(arcGen({
					startAngle: angle,
					endAngle: nextAngle
				}))
				angle = nextAngle
			}
			return arcs
		},
		rowOuterWedges: function() {
			const arcGen = d3.arc()
				.innerRadius(this.innerRadius)
				.outerRadius(this.outerRadius)
			const arcs = []
			let angle = 0
			for(let i = 0; i < this.rows.length; i += 1) {
				arcs.push([])
				for(let j = 0; j < this.cols.length; j += 1) {
					const nextAngle = angle + 2*Math.PI*(this.matrix[i][j]/this.total)
					arcs[i].push(arcGen({
						startAngle: angle,
						endAngle: nextAngle
					}))
					angle = nextAngle
				}
			}
			return arcs
		},
		colInnerWedges: function() {
			const arcGen = d3.arc()
				.innerRadius(0)
				.outerRadius(this.innerRadius)
			const arcs = []
			let angle = 0
			for(let j = 0; j < this.cols.length; j += 1) {
				const nextAngle = angle + 2*Math.PI*(this.colSums[j]/this.total)
				arcs.push(arcGen({
					startAngle: angle,
					endAngle: nextAngle
				}))
				angle = nextAngle
			}
			return arcs
		},
		colOuterWedges: function() {
			const arcGen = d3.arc()
				.innerRadius(this.innerRadius)
				.outerRadius(this.outerRadius)
			const arcs = []
			let angle = 0
			for(let j = 0; j < this.cols.length; j += 1) {
				arcs.push([])
				for(let i = 0; i < this.rows.length; i += 1) {
					const nextAngle = angle + 2*Math.PI*(this.matrix[i][j]/this.total)
					arcs[j].push(arcGen({
						startAngle: angle,
						endAngle: nextAngle
					}))
					angle = nextAngle
				}
			}
			return this.transpose(arcs)
		},
	},
}


</script>