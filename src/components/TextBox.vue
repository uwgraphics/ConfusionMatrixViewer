<template>
	<g class="TextBox">
		<rect 
			:x=x
			:y=y
			:width=width
			:height=height
			:fill=rfill
			:stroke=stroke
			:pointer-events="unclickable?'none':'auto'">
		</rect>
		<text
			font-family="Arial"
			text-anchor="middle"
			dominant-baseline="middle"
			:x=tx
			:y=ty
			:font-size=fontSize
			:fill=tfill
			:pointer-events="unclickable?'none':'auto'">
			{{ text }}
		</text>
	</g>
</template>

<script>
export default {
	name: 'TextBox',
	props: ['x', 'y', 'width', 'height', 'rfill', 'stroke', 'text', 'tfill', 'unclickable'],
	computed: {
		fontSize: function() {
			return Math.min(this.width/(1 + String(this.text).length)*2.7, this.height)*0.75
		},
		tx: function() {
			return this.x + this.width/2
		},
		ty: function() {
			return this.y + this.height/2 + this.fontSize/8
		},
	}
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