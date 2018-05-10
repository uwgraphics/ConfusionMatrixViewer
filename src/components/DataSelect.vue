<template>
	<div class="DataSelect">
		<h4>Dataset: <select v-model="dataSelect">
			<option>simple</option>
			<option>digits</option>
			<option>imported</option>
		</select>
		</h4>
		<h4>
			<label>Import data: 
				<input type="file" @change="fileLoad">
				</input>
			</label>
			<font color="red">
				{{ errorText }}
			</font>
			<button @click="openInstructions">
				Formatting Instructions
			</button>
		</h4>
		<views :dataset=currentDataset>
		</views>
	</div>
</template>

<script>
import * as d3 from 'd3'

export default {
	name: 'DataSelect',
	props: [],
	data: function() {
		const defaultRowLabels = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
		const defaultColLabels = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J']
		let d = -0.3
		const baseColors = ['#8dd3c7',
			'#ffffb3', '#bebada', '#fb8072',
			'#80b1d3', '#fdb462', '#b3de69',
			'#fccde5', '#d9d9d9', '#bc80bd']
		const defaultRowColors = baseColors.map(color => d3.rgb(color).darker(-0.3))
		const defaultColColors = baseColors.map(color => d3.rgb(color).darker(0.5))
		return {
			errorText: '',
			dataSelect: 'simple',
			defaultRowLabels,
			defaultColLabels,
			defaultRowColors,
			defaultColColors,
			simple: {
				matrix: [[100, 5, 0], [10, 5, 0], [1, 5, 0], [0, 5, 1]],
				rows: [{label: 'A', color: '#fc7f03'},
					   {label: 'B', color: '#fc3903'},
					   {label: 'C', color: '#d1024e'},
					   {label: 'D', color: '#ae700a'}],
				cols: [{label: 'α', color: '#d6fffb'},
					   {label: 'β', color: '#b3e3f4'},
					   {label: 'γ', color: '#a2faa3'}],
		    },
		    digits: {
				matrix: [[177, 3, 0, 0, 0, 0, 0, 0, 0, 1],
						 [2, 101, 9, 47, 4, 3, 4, 4, 0, 0],
						 [2, 99, 55, 0, 1, 24, 1, 0, 0, 0],
						 [0, 1, 20, 138, 6, 0, 7, 8, 0, 0],
						 [1, 0, 0, 41, 136, 0, 2, 0, 2, 0],
						 [0, 8, 2, 2, 0, 148, 13, 3, 0, 1],
						 [0, 7, 0, 11, 2, 1, 156, 6, 0, 0],
						 [0, 2, 2, 0, 5, 0, 0, 170, 0, 0],
						 [0, 2, 3, 0, 0, 0, 0, 10, 166, 0],
						 [0, 0, 0, 0, 0, 0, 0, 0, 2, 176]],
				rows:  ['6', '8', '1', '9', '5', '2', '3', '7', '4', '0'].map(function(d, i) {
					return {
						label: d,
						color: defaultRowColors[i],
					}
				}),
				cols: defaultColLabels.map(function(d, i) {
					return {
						label: d,
						color: defaultColColors[i],
					}
				}),
			},
			imported: {
				matrix: null,
				rows: null,
				cols: null,
			}
		}
	},
	computed : {
		currentDataset: function() {
			if(this.dataSelect === 'simple') {
				return this.simple
			}
			else if(this.dataSelect === 'digits') {
				return this.digits
			}
			else if(this.dataSelect === 'imported') {
				if(this.imported.matrix == null){
					this.errorText = "No data loaded!"
					return this.simple
				} else {
					return this.imported
				}
			}
		},
	},
	methods: {
		fileLoad: function() {
			const f = event.target.files[0]
			const reader = new FileReader();
			
			const that = this
			
			reader.onload = function(event) {
				const text = reader.result
				try {
					const inputData = JSON.parse(text)
					that.errorText = that.validate(inputData)
				}
				catch(error) {
					that.errorText = 'Invalid JSON format. See console for details'
					console.error(error)
				}
			}
			reader.readAsText(f)
		},
		validate: function(inputData) {
			let {matrix, rows, cols} = inputData
			const numRows = matrix.length;
			const numCols = matrix[0].length;
			
			for(let i = 1; i < numRows; i += 1) {
				if(numRows != inputData.matrix[i].length) {
					return "Input matrix not rectangular!"
				}
			}

			if(rows) {
				if(rows.length != numRows) {
					return `Expected rows to have ${numRows} elements, had ${rows.length}!`
				} else {
					// Count number of valid labels
					const labelsCount = rows.filter((a) => a.label != undefined).length
					if(labelsCount == 0) {
						for(let i = 0; i < numRows; i += 1) {
							rows[i].label = this.defaultRowLabels[i]
						}
					}
					else if(labelsCount < numRows) {
						return "Only some rows have labels!"
					}
					// Count number of valid colors
					const colorsCount = rows.filter((a) => a.color != undefined).length
					if(colorsCount == 0) {
						for(let i = 0; i < numRows; i += 1) {
							rows[i].color = this.defaultRowColors[i]
						}
					}
					else if(colorsCount < numRows) {
						return "Only some rows have colors!"
					}
				}
			} else {
				if(numRows > 10) {
					return "Above maximum supported number of rows! To use more than 10 rows/columns, include your own colors."
				} else {
					rows = []
					for(let i = 0; i < numRows; i += 1) {
						rows.push({
							label: this.defaultRowLabels[i],
							color: this.defaultRowColors[i],
						})
					}
				}
			}
			
			if(cols) {
				if(cols.length != numCols) {
					return `Expected cols to have ${numCols} elements, had ${cols.length}!`
				} else {
					// Count number of valid labels
					const labelsCount = cols.filter((a) => a.label != undefined).length
					if(labelsCount == 0) {
						for(let j = 0; j < numCols; j += 1) {
							cols[j].label = this.defaultColLabels[j]
						}
					}
					else if(labelsCount < numCols) {
						return "Only some cols have labels!"
					}
					// Count number of valid colors
					const colorsCount = cols.filter((a) => a.color != undefined).length
					if(colorsCount == 0) {
						for(let j = 0; j < numCols; j += 1) {
							cols[j].color = this.defaultColColors[j]
						}
					}
					else if(colorsCount < numCols) {
						return "Only some cols have colors!"
					}
				}
			} else {
				if(numCols > 10) {
					return "Above maximum supported number of cols! To use more than 10 rows/columns, include your own colors."
				} else {
					cols = []
					for(let i = 0; i < numCols; i += 1) {
						cols.push({
							label: this.defaultColLabels[i],
							color: this.defaultColColors[i],
						})
					}
				}
			}
			this.imported.matrix = matrix
			this.imported.rows = rows
			this.imported.cols = cols
			this.dataSelect = 'imported'
			return ''
		},
		openInstructions: function() {
			window.open().document.write(
				"<h1>Formatting</h1>" +
				"<h3>This program can only take json input</h3>" +
				"<h4>Minimum example</h4>" +
				"<div>{\"matrix\": [[1, 0, 0], [0, 1, 0], [0, 0, 1]]}</div>" +
				"<h4>This gives default colors and labels to each of the rows and colums.</h4>" +
				"<h4>You can include custom colors and/or labels as in the following example:</h4>" +
				"<div>{</div>" +
				"<div>\"matrix\": [[1, 0, 0], [0, 1, 0], [0, 0, 1]],</div>" +
				"<div>\"rows\": [{\"label\": \"A\", \"color\": \"#fc7f03\"},{\"label\": \"B\", \"color\": \"red\"},{\"label\": \"C\", \"color\": \"#d1024e\"}],</div>" +
				"<div>\"cols\": [{\"label\": \"α\"},{\"label\": \"β\"},{\"label\": \"γ\"}]</div>" +
				"<div>}</div>")
		}
	},
}
</script>

<style>
h4 {
	-webkit-user-select:none;
	-khtml-user-select:none;
	-moz-user-select:none;
	-ms-user-select:none;
	-o-user-select:none;
	user-select:none;
}

</style>