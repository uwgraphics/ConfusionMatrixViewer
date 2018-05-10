<template>
	<div class="Bipartite">
		<svg viewBox="0 0 200 200">
			<!-- Connections -->
			<g v-for="(r, i) in rows">
				<polygon v-for="(c, j) in cols" @click="$emit('select', [i, j])"
					:points=connectionPoints[i][j]
					:fill=colorRamp(connectionStrengths[i][j])
					:stroke="(i==row&&j==col)?'yellow':'none'">
				</polygon>
			</g>
			<!-- Black line down selected connection -->
			<line
				stroke="black"
				pointer-events="none"
				:x1=scale/2
				:y1=rowYs[row]
				:x2=width-scale/2
				:y2=colYs[col]>
			</line>
			<!-- Row Circles Left --> 
			<text-ball v-for="(r, i) in rows" @click.native="$emit('select', [i, -1])"
				:cx=scale/2
				:cy=rowYs[i]
				:r=scale*circleSize/2
				:cfill=r.color
				:text=r.label
				:stroke="(rowSelected&&i==row)?'yellow':null"
				:stroke-width=2*st>
			</text-ball>
			<!-- Column Circles Right --> 
			<text-ball v-for="(c, j) in cols" @click.native="$emit('select', [-1, j])"
				:cx=width-scale/2
				:cy=colYs[j]
				:r=scale*circleSize/2
				:cfill=c.color
				:text=c.label
				:stroke="(!rowSelected&&j==col)?'yellow':null"
				:stroke-width=2*st>
			</text-ball>
			<!-- Additional Black Stroke on Selected Circle -->
			<circle
				fill="transparent"
				stroke="black"
				:cx="rowSelected?(scale/2):(width-scale/2)"
				:cy="rowSelected?(rowYs[row]):(colYs[col])"
				:r=scale*circleSize/2+st>
			</circle>
			<!-- Black Stroke on Selected Connection's Other Circle -->
			<circle
				fill="transparent"
				stroke="black"
				:cx="rowSelected?(width-scale/2):(scale/2)"
				:cy="rowSelected?(colYs[col]):(rowYs[row])"
				:r=scale*circleSize/2>
			</circle>
			<!-- Color Legend on bottom -->
			<color-legend
				text="Intersection over Union (IOU)"
				:x=0
				:y=height*(1-margin)
				:width=width
				:height=height*margin
				:resolution=200
				:colorRamp=colorRamp>
			</color-legend>
		</svg>
	</div>
</template>

<script>
import * as d3 from 'd3'

export default {
	name: 'Bipartite',
	props: ['matrix', 'rows', 'cols', 'row', 'col', 'rowSelected'],
	data: function() {
		const width = 200
		const height = 200
		return {
			width,
			height,
			margin: 0.1,
			colorRamp: d3.interpolatePlasma,
			circleSize: 0.7,
			connectionThickness: 1/5,
			st: Math.min(width, height)/100,
		}
	},
	computed: {
		rowSums: function() {
			return this.matrix.map(x => x.reduce((a, b) => a + b))
		},
		colSums: function() {
			return this.transpose(this.matrix).map(x => x.reduce((a, b) => a + b))
		},
		max: function() {
			return this.matrix.map(x => x.reduce((a, b) => Math.max(a, b))).reduce((a, b) => Math.max(a, b))
		},
		scale: function() {
			return this.height/Math.max(this.rows.length, this.cols.length)*(1 - this.margin)
		},
		rowYs: function() {
			const Ys = []
			for(let i = 0; i < this.rows.length; i += 1) {
				Ys.push(this.scale*(i - this.rows.length/2 + 1/2) + this.width/2*(1 - this.margin))
			}
			return Ys
		},
		colYs: function() {
			const Ys = []
			for(let j = 0; j < this.cols.length; j += 1) {
				Ys.push(this.scale*(j - this.cols.length/2 + 1/2) + this.height/2*(1 - this.margin))
			}
			return Ys
		},
		connectionPoints: function() {
			const points = []
			for(let i = 0; i < this.rows.length; i += 1) {
				points.push([])
				for(let j = 0; j < this.cols.length; j += 1) {
					const v = this.matrix[i][j]
					const s = this.scale*this.connectionThickness*v/2
					
					const x1 = this.scale/2
					const x2 = this.scale/2
					const x3 = this.width - this.scale/2
					const x4 = this.width - this.scale/2
					
					const y1 = this.rowYs[i] - s/this.rowSums[i]
					const y2 = this.rowYs[i] + s/this.rowSums[i]
					const y3 = this.colYs[j] + s/this.colSums[j]
					const y4 = this.colYs[j] - s/this.colSums[j]
					points[i].push(`${x1} ${y1}, ${x2} ${y2}, ${x3} ${y3}, ${x4} ${y4}`)
				}
			}
			return points
		},
		connectionStrengths: function() {
			const ious = [] // Intersection over union
			for(let i = 0; i < this.rows.length; i += 1) {
				ious.push([])
				for(let j = 0; j < this.cols.length; j += 1) {
					const v = this.matrix[i][j]
					ious[i].push(v/(this.rowSums[i] + this.colSums[j] - v))
				}
			}
			return ious
		}
	},
}


</script>