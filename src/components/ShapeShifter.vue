<template>
	<div class="ShapeShifter">
		<h2>{{ title }}
			<select v-model="selectedComponent">
				<option v-for="choice in selection">
					{{ choice }}
				</option>
			</select>
		</h2>
		<component @select="$emit('select', $event)"
			:is=selectedComponent
			:matrix=matrix
			:rows=rows
			:cols=cols
			:row=row
			:col=col
			:rowSelected=rowSelected>
		</component>
	</div>
</template>

<script>
import * as d3 from 'd3'

export default {
	name: 'ShapeShifter',
	props: ['matrix', 'rows', 'cols', 'row', 'col', 'rowSelected', 'selection', 'title'],
	data: function() {
		return {
			selectedComponent: this.selection[0]
		}
	}
}


</script>