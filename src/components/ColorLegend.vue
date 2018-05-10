<template>
	<g class="ColorLegend">
		<!-- Title -->
		<text
			font-family="Arial"
			text-anchor="middle"
			:x=x+width/2
			:y=textY
			:font-size=height/4>
			{{ text }}
		</text>
		<!-- Scale Text-->
		<text
			font-family="Arial"
			text-anchor="beginning"
			:x=x
			:y=textY
			:font-size=height/4>
			0
		</text>
		<text
			font-family="Arial"
			text-anchor="end"
			:x=x+width
			:y=textY
			:font-size=height/4>
			1
		</text>
		<!-- Color Scale -->
		<rect v-for="i in resolution"
			:x=x+(i-1)*width/resolution
			:y=y+height/2
			:width=width/resolution+1
			:height=height/2
			:fill=colorRamp(i/resolution)>
		</rect>
	</g>
</template>

<script>

export default {
	name: 'ColorLegend',
	props: ['x', 'y', 'width', 'height', 'resolution', 'colorRamp', 'text'],
	computed: {
		textY: function() {
			return this.y + this.height*3/7
		}
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