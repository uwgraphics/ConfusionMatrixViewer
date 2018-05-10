<template>
	<div class="Marimekko">
		<svg viewBox="0 0 200 200">
			<!-- Row Selected View -->
			<template v-if="rowSelected">
				<!-- Row Label -->
				<text-box
					stroke="black"
					:x=1
					:y=rowYs[row]
					:width=mw
					:height=rowYs[row+1]-rowYs[row]
					:rfill=rows[row].color
					:text=rows[row].label>
				</text-box>
				<!-- Unselected Rows --> 
				<g v-for="(r, i) in rows">
					<rect v-for="(c, j) in cols" @click="$emit('select', [i, j])"
						:x=rowXs[i][j]
						:y=rowYs[i]
						:width=rowXs[i][j+1]-rowXs[i][j]
						:height=rowYs[i+1]-rowYs[i]
						:fill=c.color
						:opacity=1-dim>
					</rect>
				</g>
				<!-- Selected Row -->
				<text-box v-for="(c, j) in cols"
					stroke="black"
					:unclickable=true
					:x=rowXs[row][j]
					:y=rowYs[row]
					:width=rowXs[row][j+1]-rowXs[row][j]
					:height=rowYs[row+1]-rowYs[row]
					:rfill=c.color
					:stroke-width="(j==col)?2:1"
					:text="matrix[row][j]">
				</text-box>
			</template>
			<!-- Column Selected View -->
			<template v-else>
				<!-- Col Label -->
				<text-box
					stroke="black"
					:x=colXs[col]
					:y=1
					:width=colXs[col+1]-colXs[col]
					:height=mh
					:rfill=cols[col].color
					:text=cols[col].label>
				</text-box>
				<!-- Unselected Cols --> 
				<g v-for="(c, j) in cols"> 
					<rect v-for="(r, i) in rows" @click="$emit('select', [i, j])"
						:x=colXs[j]
						:y=colYs[i][j]
						:width=colXs[j+1]-colXs[j]
						:height=colYs[i+1][j]-colYs[i][j]
						:fill=r.color
						:opacity=1-dim>
					</rect>
				</g>
				<!-- Selected Row -->
				<text-box v-for="(r, i) in rows"
					stroke="black"
					:unclickable=true
					:x=colXs[col]
					:y=colYs[i][col]
					:width=colXs[col+1]-colXs[col]
					:height=colYs[i+1][col]-colYs[i][col]
					:rfill=r.color
					:stroke-width="(i==row)?2:1"
					:text="matrix[i][col]">
				</text-box>
			</template>
			
		</svg>
	</div>
</template>

<script>

export default {
	name: 'Marimekko',
	props: ['matrix', 'rows', 'cols', 'row', 'col', 'rowSelected'],
	data: function() {
		const width = 200
		const height = 200
		return {
			width,
			height,
			mw: width/10,
			mh: height/10,
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
		rowXs: function() {
			const Xs = []
			for(let i = 0; i < this.rows.length; i += 1) {
				Xs.push([this.mw])
				for(let j = 0; j < this.cols.length; j += 1) {
					Xs[i].push(Xs[i][j] + (this.width - this.mw - 2)*this.matrix[i][j]/this.rowSums[i])
				}
			}
			return Xs
		},
		rowYs: function() {
			const Ys = [this.mh]
			for(let i = 0; i < this.rows.length; i += 1) {
				Ys.push(Ys[i] + (this.height - this.mh - 2)*this.rowSums[i]/this.total)
			}
			return Ys
		},
		colXs: function() {
			const Xs = [this.mw]
			for(let j = 0; j < this.cols.length; j += 1) {
				Xs.push(Xs[j] + (this.width - this.mw - 2)*this.colSums[j]/this.total)
			}
			return Xs
		},
		colYs: function() {
			const Ys = []
			for(let j = 0; j < this.cols.length; j += 1) {
				Ys.push([this.mw])
				for(let i = 0; i < this.rows.length; i += 1) {
					Ys[j].push(Ys[j][i] + (this.height - this.mh - 2)*this.matrix[i][j]/this.colSums[j])
				}
			}
			return this.transpose(Ys)
		},
	},
}


</script>