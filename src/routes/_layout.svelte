<script>
	import Nav from "../components/Nav.svelte";

	export let segment;

	import { onMount } from "svelte";

	function draw() {
		function createBackground(rectWidth, width, height) {
			ctx.fillStyle = "#202020";
			for (let i = 0; i < width; i += 13) {
				for (let j = 0; j < height; j += 13) {
					ctx.fillRect(i, j, rectWidth, rectWidth);
				}
			}
		}

		function setCanvasDimensions(ctx) {
			var body = document.body,
				html = document.documentElement;
			var height = Math.max(
				body.scrollHeight,
				body.offsetHeight,
				html.clientHeight,
				html.scrollHeight,
				html.offsetHeight
			);
			var width = window.innerWidth;
			ctx.canvas.width = width;
			ctx.canvas.height = height;

			return [ctx, width, height];
		}

		var canvas = document.getElementById("canvas");
		if (canvas.getContext) {
			var ctx = canvas.getContext("2d");

			var canvasWidth;
			var canvasHeight;
			var rectWidth = 2;
			[ctx, canvasWidth, canvasHeight] = setCanvasDimensions(ctx);
			createBackground(rectWidth, canvasWidth, canvasHeight);
		}

		window.addEventListener("resize", () => {
			[ctx, canvasWidth, canvasHeight] = setCanvasDimensions(ctx);
			createBackground(rectWidth, canvasWidth, canvasHeight);
		});
	}

	onMount(draw);
</script>

<!-- <Nav {segment}/> -->

<main>
	<canvas id="canvas" />
	<slot />
</main>

<style>
	canvas {
		position: absolute;
		z-index: -1;
	}
</style>
