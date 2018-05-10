<template>
	<div>
		<section class="wide">
			<shape-shifter @select="update($event)"
				title="Full View"
				:matrix=dataset.matrix 
				:rows=dataset.rows 
				:cols=dataset.cols 
				:row=row 
				:col=col
				:rowSelected=rowSelected
				:selection="['matrix', 'marker-matrix']">
			</shape-shifter>
		</section>
		<section class="narrow">
			<shape-shifter @select="update($event)"
				title="Decomposition View"
				:matrix=dataset.matrix 
				:rows=dataset.rows 
				:cols=dataset.cols 
				:row=row 
				:col=col
				:rowSelected=rowSelected
				:selection="['marimekko', 'pie']">
			</shape-shifter>
			
			<shape-shifter @select="update($event)"
				title="Intersection View"
				:matrix=dataset.matrix 
				:rows=dataset.rows 
				:cols=dataset.cols 
				:row=row 
				:col=col
				:rowSelected=rowSelected
				:selection="['venn']">
			</shape-shifter>
		</section>
		<section class="wide">
			<shape-shifter @select="update($event)"
				title="Match View"
				:matrix=dataset.matrix 
				:rows=dataset.rows 
				:cols=dataset.cols 
				:row=row 
				:col=col
				:rowSelected=rowSelected
				:selection="['bipartite']">
			</shape-shifter>
		</section>
	</div>
</template>

<script>
export default {
	name: 'Views',
	props: ['dataset'],
	data: function() {
		return {
			matrix: [[100, 5, 0], [10, 5, 0], [1, 5, 0], [0, 5, 1]],
			rows: [{label: 'A', color: '#fc7f03'},
				   {label: 'B', color: '#fc3903'},
				   {label: 'C', color: '#d1024e'},
				   {label: 'D', color: '#ae700a'}],
			cols: [{label: 'α', color: '#d6fffb'},
				   {label: 'β', color: '#b3e3f4'},
				   {label: 'γ', color: '#a2faa3'}],
			row: 0,
			col: 0,
			rowSelected: true,
		}
	},
	methods: {
		update: function(d) {
			const [i, j] = d
			if(i == this.row && j == this.col) {
				this.rowSelected = !this.rowSelected
			} else {
				if(i >= 0) {
					this.row = i
				} else {
					this.rowSelected = false
				}
				if(j >= 0) {
					this.col = j
				} else {
					this.rowSelected = true
				}
			}
		}
	},
	watch: {
		dataset: function() {
			this.row = 0
			this.col = 0
			this.rowSelected = true
		}
	}
}
</script>

<style>
section {
	float: left;
	margin: 0 1.5%;
}
.wide {
	width: 36%;
}

.narrow {
	width: 18%;
}

h2 {
	-webkit-user-select:none;
	-khtml-user-select:none;
	-moz-user-select:none;
	-ms-user-select:none;
	-o-user-select:none;
	user-select:none;
}

</style>