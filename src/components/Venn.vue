<template>
	<div class="Venn">
		<svg viewBox="0 0 300 300">
			<!-- Row rectangle -->
			<rect @click="$emit('select', [row, -1])"
				:x=margin
				:y=margin
				:width=rS
				:height=rS
				:fill=rows[row].color
			>
			</rect>
			
			<!-- Column rectangle -->
			<rect @click="$emit('select', [-1, col])"
				:x=tS-cS+margin
				:y=tS-cS+margin
				:width=cS
				:height=cS
				:fill=cols[col].color>
			</rect>
			
			<!-- Intersection rectangle -->
			<text-box @click.native="$emit('select', [row, col])" v-if="iS > 0"
				:x=tS-cS+margin
				:y=tS-cS+margin
				:width=iS
				:height=iS
				:rfill=intColor
				:text=matrix[row][col]>
			</text-box>
			
			<!-- Row rectangle text -->
			<text
				font-family="Arial"
				text-anchor="beginning"
				text-baseline="bottom"
				:x=margin
				:y=margin*3/4
				:font-size=margin>
				{{rowSize - intSize}}
			</text>
			
			<!-- Col rectangle text -->
			<text
				font-family="Arial"
				text-anchor="end"
				text-baseline="top"
				:x=margin+tS
				:y=margin+tS+margin
				:font-size=margin>
				{{colSize - intSize}}
			</text>
			
			<!-- Highlight Stroke -->
			<rect
				stroke="black"
				stroke-width="2"
				fill="transparent"
				pointer-events="none"
				:x="(rowSelected)?(margin):(margin+rS-iS)"
				:y="(rowSelected)?(margin):(margin+rS-iS)"
				:width="rowSelected?rS:cS"
				:height="rowSelected?rS:cS">
			</rect>
		</svg>
	</div>
</template>

<script>
import * as d3 from 'd3'

export default {
	name: 'Venn',
	props: ['matrix', 'rows', 'cols', 'row', 'col', 'rowSelected'],
	data: function () {
		const height = 300
		const width = 300
		const size = Math.min(height, width)
		const margin = size/10
		const tS = size - 2*margin
		const st = size/100 // stroke-width/2
		return {
			margin, tS, st
		}
	},
	computed: {
		rowSize: function() {
			return this.matrix[this.row].reduce((a, b) => a + b)
		},
		colSize: function() {
			return this.transpose(this.matrix)[this.col].reduce((a, b) => a + b)
		},
		intSize: function() {
			return this.matrix[this.row][this.col]
		},
		scale: function() {
			return this.tS/(Math.sqrt(this.rowSize) + Math.sqrt(this.colSize) - Math.sqrt(this.intSize))
		},
		rS: function() {
			return Math.sqrt(this.rowSize)*this.scale
		},
		cS: function() {
			return Math.sqrt(this.colSize)*this.scale
		},
		iS: function() {
			return Math.sqrt(this.intSize)*this.scale
		},
		intColor: function() {
			return d3.interpolateRgb(this.rows[this.row].color,this.cols[this.col].color)(0.5)
		},
	},
}
</script>

<style scoped>
text {
	-webkit-user-select:none;
	-khtml-user-select:none;
	-moz-user-select:none;
	-ms-user-select:none;
	-o-user-select:none;
	user-select:none;
}
</style>