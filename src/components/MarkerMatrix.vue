<template>
	<base-matrix @select="$emit('select', $event)"
		cellType="marker-matrix-cell"
		:matrix=matrix 
		:rows=rows 
		:cols=cols 
		:row=row 
		:col=col
		:rowSelected=rowSelected>
	</base-matrix>
</template>

<script>
export default {
	name: 'Matrix',
	props: ['matrix', 'rows', 'cols', 'row', 'col', 'rowSelected'],
}
</script>