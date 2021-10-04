<script context="module">
	export async function preload(page, session) {
		const res = await this.fetch(
			"https://slibotechnyapi.pythonanywhere.com/get_strany_and_first",
			{
				method: "GET",
				headers: {
					"content-type": "application/json",
				},
			}
		);
		const json = await res.json();
		let strany = json.strany;

		return { strany };
	}
</script>

<script>
	import { onMount } from "svelte";

	import Logo from "../components/Logo.svelte";
	import BodProgramu from "../components/BodProgramu.svelte";

	onMount(() => {
		setTimeout(getAllData, 1);
	});

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

	async function getAllData() {
		const res = await fetch(
			"https://slibotechnyapi.pythonanywhere.com/get_strany_and_body_programu",
			{
				method: "GET",
				headers: {
					"content-type": "application/json",
				},
			}
		);
		const json = await res.json();
		strany = json.strany;

		for (const strana of strany) {
			for (const bp of strana.body_programu) {
				bp["otevrit"] = false;
			}
		}
	}

	export let strany = [];
	let chosenStrana = 1;
</script>

<Logo />

<section>
	<div id="flex">
		{#each strany as strana, index}
			{#if index < 10}
				<div
					class="strana-clickable {index == chosenStrana && 'active'}"
					style="background-color: {strana.barva}; color: {strana.sekundarni_barva};"
					on:click={() => {
						chosenStrana = index;
					}}
				>
					{strana.nazev}
				</div>
			{/if}
		{/each}
	</div>
	<div id="mobile-flex">
		<select
			name=""
			id=""
			bind:value={chosenStrana}
			style="background-color: {strany[chosenStrana].barva};
			 	   color: {strany[chosenStrana].sekundarni_barva};"
		>
			{#each strany as strana, index}
				<option
					value={index}
					style="background-color: {strana.barva};
							color: {strana.sekundarni_barva};">{strana.nazev}</option
				>
			{/each}
		</select>
	</div>

	<div
		id="body-programu"
		style="background-color: {strany[chosenStrana].barva}; 
		color: {strany[chosenStrana].sekundarni_barva};
		"
	>
		<div class="flex">
			<div>
				<img src="/Filtr.png" alt="Filtr" />
				<span>Zobrazit filtry a řazení</span>
			</div>
			<div>
				<span>Na co se to vlastně dívám?</span>
				<img src="/QuestionMark.png" alt="Otazník" />
			</div>
		</div>
		<h3>Body programu</h3>
		{#if strany[chosenStrana].body_programu}
			{#each strany[chosenStrana]?.body_programu as bp}
				<BodProgramu {bp} barva={strany[chosenStrana].barva} />
			{/each}
		{/if}
	</div>
</section>

<style>
	section {
		padding: 0px calc((100% - 90rem) / 2) 0px calc((100% - 90rem) / 2);
	}

	#flex {
		display: flex;
		justify-content: space-between;
		margin-top: 50px;
	}
	.strana-clickable {
		overflow: hidden;
		height: 50px;
		line-height: 50px;
		width: 8%;

		border-radius: 5px 5px 0 0;

		text-align: center;
		font-family: "Rokkitt";
		font-size: 26px;

		margin-bottom: 10px;

		cursor: pointer;
		transition: 0.2s;
	}

	.strana-clickable:hover {
		filter: brightness(75%);
		-webkit-filter: brightness(75%);
	}

	.active {
		height: 60px;
		line-height: 65px;
		font-size: 35px;
		margin-bottom: 0;

		cursor: auto;
	}
	.active:hover {
		filter: brightness(100%);
		-webkit-filter: brightness(100%);
	}

	#body-programu {
		width: 100%;
		border-radius: 5px;
		padding: 20px 20px;
		margin-bottom: 30px;
		box-sizing: border-box;
		transition: 0.15s;
		overflow-y: hidden;
		overflow-x: hidden;
	}

	.flex {
		display: flex;
		justify-content: space-between;
	}
	.flex div {
		transition: 0.1s;
		cursor: pointer;
	}
	.flex div:hover span {
		filter: brightness(75%);
		-webkit-filter: brightness(75%);
	}
	.flex img {
		vertical-align: middle;
	}
	.flex span {
		vertical-align: middle;
		text-decoration: underline;
		text-decoration-thickness: 0.1px;
		transition: 0.15s;
		font-size: 14px;
		font-weight: bold;
	}
	.flex div:nth-child(1) span {
		margin-left: 8px;
	}
	.flex div:nth-child(2) span {
		margin-right: 8px;
	}

	h3 {
		font-size: 26px;
		font-weight: 500;
	}

	#mobile-flex {
		display: none;
	}

	select {
		margin: 20px 0;
		width: 100%;
		padding: 5px 35px 5px 5px;

		font-size: 24px;
		font-weight: 700;
		font-family: "Rokkitt";

		height: 50px;
		border-radius: 5px;
		border: 0;
	}

	/* CAUTION: Internet Explorer hackery ahead */

	select::-ms-expand {
		display: none; /* Remove default arrow in Internet Explorer 10 and 11 */
	}

	/* Target Internet Explorer 9 to undo the custom arrow */
	@media screen and (min-width: 0\0) {
		select {
			background: none\9;
			padding: 5px\9;
		}
	}

	@media (max-width: 1500px) {
		section {
			padding: 0 30px;
		}
	}
	@media (max-width: 1025px) {
		.strana-clickable {
			font-size: 22px;
			height: 40px;
			line-height: 40px;
		}
		.active {
			font-size: 25px;
			height: 50px;
			line-height: 55px;
		}
	}
	@media (max-width: 800px) {
		section {
			padding: 0 10px;
		}
		.strana-clickable {
			font-size: 18px;
		}
		.active {
			font-size: 21px;
		}
		#body-programu {
			padding: 20px 10px;
		}
	}
	@media (max-width: 700px) {
		#flex {
			display: none;
		}

		#mobile-flex {
			display: block;
		}
	}
</style>
