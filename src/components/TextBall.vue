<template>
	<g class="TextBall">
		<circle 
			:cx=cx
			:cy=cy
			:r=r
			:fill=cfill
			:stroke=stroke
			:pointer-events="unclickable?'none':'auto'">
		</circle>
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
	name: 'TextBall',
	props: ['cx', 'cy', 'r', 'cfill', 'stroke', 'text', 'tfill', 'unclickable'],
	computed: {
		fontSize: function() {
			return Math.min(this.r*2/(1 + String(this.text).length)*2.7, this.r*2)*0.5
		},
		tx: function() {
			return this.cx
		},
		ty: function() {
			return this.cy + this.fontSize/12
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