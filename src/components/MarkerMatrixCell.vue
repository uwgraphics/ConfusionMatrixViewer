<template>
	<g class="MarkerMatrixCell">
	<text-ball
		:cx=x+width/2
		:cy=y+height/2
		:r="Math.sqrt(text/max)*(Math.min(width, height)-5*st)/2"
		:cfill=rfill
		:stroke=stroke
		:text=text
		:tfill=tfill>
	</text-ball>
	<rect
		fill="transparent"
		:x=x
		:y=y
		:width=width
		:height=height>
	</rect>
	</g>
</template>

<script>
export default {
	name: 'MarkerMatrixCell',
	props: ['x', 'y', 'width', 'height', 'rfill', 'stroke', 'text', 'tfill', 'max', 'st'],
}
</script>