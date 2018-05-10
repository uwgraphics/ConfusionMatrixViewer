<template>
	<div class="BaseMatrix">
		<svg viewBox="0 0 500 500">
			<!-- Row Labels -->
			<text-box v-for="(label, i) in rowLabelInfo"
				@click.native="$emit('select', [i, -1])"
				v-bind="label"
			>
			</text-box>
		
			<!-- Column Labels -->
			<text-box v-for="(label, j) in colLabelInfo"
				@click.native="$emit('select', [-1, j])"
				v-bind="label"
			>
			</text-box>
		
			<!-- Right Bar Chart -->
			<text-box v-for="(bar, i) in rowBarChartInfo"
				@click.native="$emit('select', [i, -1])"
				v-bind="bar"
			>
			</text-box>
			
			<!-- Bottom Bar Chart -->
			<text-box v-for="(bar, j) in colBarChartInfo"
				@click.native="$emit('select', [-1, j])"
				v-bind="bar"
			>
			</text-box>
			
			<!-- Matrix Cells -->
			<component v-for="(cell, index) in matrixCellInfo"
				@click.native="$emit('select', [Math.floor(index/cols.length), index%cols.length])"
				v-bind="cell"
				:is=cellType
				:max=max
				:st=st
			>
			</component>
			
			<!-- Total Text -->
			<text-box v-bind="totalTextInfo">
			</text-box>
			
			<!-- Selected Bar -->
			<rect v-if="rowSelected"
				fill="transparent"
				stroke="yellow"
				pointer-events="none"
				:x=highlightInfo.x2
				:y=highlightInfo.y1
				:width=highlightInfo.width2
				:height=highlightInfo.height1
				:stroke-width=2*st>
			</rect>
			<rect v-else
				fill="transparent"
				stroke="yellow"
				pointer-events="none"
				:x=highlightInfo.x1
				:y=highlightInfo.y2
				:width=highlightInfo.width1
				:height=highlightInfo.height2
				:stroke-width=2*st>
			</rect>
			<rect
				fill="transparent"
				stroke="orange"
				pointer-events="none"
				:x=highlightInfo.x1
				:y=highlightInfo.y1
				:width=highlightInfo.width1
				:height=highlightInfo.height1
				:stroke-width=2*st>
			</rect>
			
		</svg>
	</div>
</template>

<script>
import * as d3 from 'd3'

export default {
	name: 'BaseMatrix',
	props: ['matrix', 'rows', 'cols', 'row', 'col', 'rowSelected', 'cellType'],
	data: function() {
		const width = 500
		const height = 500
		const mw = width/10 // Margin width
		const mh = height/10
		const barThickness = 0.8 // Width of bars in side bar charts
		const strokeThickness = Math.min(width, height)/100
		const maxBar = Math.min(width, height)/5
		
		return {
			width,
			height,
			mw,
			mh,
			barThickness,
			st: strokeThickness/2,
			maxBar,
		}
	},
	computed: {
		cs: function() {
			const maxPossibleCellWidth = (this.width - this.mw - this.maxBar)/this.cols.length 
			const maxPossibleCellHeight = (this.height - this.mh - this.maxBar)/this.rows.length
			return Math.min(maxPossibleCellWidth, maxPossibleCellHeight)
		},
		rowSums: function() {
			return this.matrix.map(x => x.reduce((a, b) => a + b))
		},
		colSums: function() {
			return this.transpose(this.matrix).map(x => x.reduce((a, b) => a + b))
		},
		maxSetSum: function() {
			const maxRowSum = this.rowSums.reduce((a, b) => Math.max(a, b))
			const maxColSum = this.colSums.reduce((a, b) => Math.max(a, b))
			return Math.max(maxRowSum, maxColSum)
		},
		total: function() {
			return this.rowSums.reduce((a, b) => a + b)
		},
		max: function() {
			return this.matrix.map(x => x.reduce((a, b) => Math.max(a, b))).reduce((a, b) => Math.max(a, b))
		},
		rowLabelInfo: function() {
			const info = []
			for(let i = 0; i < this.rows.length; i += 1) {
				info.push({
					x: 0,
					y: this.mh + this.cs*i,
					width: this.mw,
					height: this.cs,
					rfill: this.rows[i].color,
					text: this.rows[i].label,
				})
			}
			return info
		},
		colLabelInfo: function() {
			const info = []
			for(let j = 0; j < this.cols.length; j += 1) {
				info.push({
					x: this.mw + this.cs*j,
					y: 0,
					width: this.cs,
					height: this.mh,
					rfill: this.cols[j].color,
					text: this.cols[j].label,
				})
			}
			return info
		},
		rowBarChartInfo: function() {
			const info = []
			for(let i = 0; i < this.rows.length; i += 1) {
				info.push({
					x: this.mw + this.cs*(this.cols.length),
					y: this.mh + this.cs*(i + (1 - this.barThickness)/2),
					width: this.maxBar*this.rowSums[i]/this.maxSetSum,
					height: this.cs*this.barThickness,
					rfill: this.rows[i].color,
					text: this.rowSums[i],
				})
			}
			return info
		},
		colBarChartInfo: function() {
			const info = []
			for(let j = 0; j < this.cols.length; j += 1) {
				info.push({
					x: this.mw + this.cs*(j + (1 - this.barThickness)/2),
					y: this.mh + this.cs*this.rows.length,
					width: this.cs*this.barThickness,
					height: this.maxBar*this.colSums[j]/this.maxSetSum,
					rfill: this.cols[j].color,
					text: this.colSums[j],
				})
			}
			return info
		},
		matrixCellInfo: function() {
			const info = []
			for(let i = 0; i < this.rows.length; i += 1) {
				for(let j = 0; j < this.cols.length; j += 1) {
					const v = this.matrix[i][j]
					info.push({
						x: this.mw + this.cs*j,
						y: this.mh + this.cs*i,
						width: this.cs,
						height: this.cs,
						rfill: d3.interpolateGreys(v/this.max),
						text: v == 0 ? "" : v,
						tfill: v/this.max > 0.7 ? "white" : "black",
						stroke: "black",
					})
				}
			}
			return info
		},
		totalTextInfo: function() {
			return {
				x: this.mw + this.cs*this.cols.length,
				y: this.mh + this.cs*this.rows.length,
				width: this.maxBar-1,
				height: this.maxBar-1,
				rfill: 'transparent',
				text: this.total,
				tfill: 'black',
			}
		},
		highlightInfo: function() {
			return {
				x1: this.mw + this.col*this.cs + this.st,
				y1: this.mh + this.row*this.cs + this.st,
				width1: this.cs - 2*this.st,
				height1: this.cs - 2*this.st,
				x2: this.st,
				y2: this.st,
				width2: this.mw + this.cols.length*this.cs - 2*this.st,
				height2: this.mh + this.rows.length*this.cs - 2*this.st,
			}
		},
	},
}


</script>