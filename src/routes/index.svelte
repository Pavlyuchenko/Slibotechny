<script context="module">
	export async function preload(page, session) {
		const res = await this.fetch(
			"https://slibotechnyapi.pythonanywhere.com/get_kategorie",
			{
				method: "GET",
				headers: {
					"content-type": "application/json",
				},
			}
		);
		const json = await res.json();
		let kategorie = json.kategorie;
		const res2 = await this.fetch(
			"https://slibotechnyapi.pythonanywhere.com/get_strany",
			{
				method: "GET",
				headers: {
					"content-type": "application/json",
				},
			}
		);
		const json2 = await res2.json();
		let strany = json2.strany;
		strany["kategorie"] = [];

		for (let strana of strany) {
			strana.kategorie = {}
			for (let kat of kategorie) {
				strana.kategorie[kat.id] = []
			}
			strana.kategorie[0] = []
		}

		return { kategorie, strany };
	}
</script>

<script>
	import Logo from "../components/Logo.svelte";
	import BodProgramu from "../components/BodProgramu.svelte";
	import { onMount } from "svelte";

	export let strany = [];
	export let kategorie = [];

	let chosenStrana = 1;
	let chosenKategorie = null;

	async function loadData (strana_id, kategorie_id) {
		if (strany[strana_id].kategorie[kategorie_id].length > 0 || strany[strana_id].kategorie[kategorie_id] === false) return

		let url;
		if (kategorie_id !== 0){
			url = "https://slibotechnyapi.pythonanywhere.com/get_some_bps/" + strana_id + "/" + kategorie_id
		} else {
			url = "https://slibotechnyapi.pythonanywhere.com/get_some_bps/" + strana_id
		}

		const res = await fetch(
			url,
			{
				method: "GET",
				headers: {
					"content-type": "application/json",
				},
			}
		);
		const json = await res.json();
		if (json.bps.length == 0) {
			strany[strana_id].kategorie[kategorie_id] = false;
			console.log(strany[strany[chosenStrana].id].kategorie[kategorie_id])
			return
		};
		strany[strana_id].kategorie[kategorie_id] = json.bps;
	}

	var isPc = true;
	onMount(() => {
		isPc = window.matchMedia("(hover: hover) and (pointer: fine)").matches
		console.log(isPc)
	})
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
						chosenKategorie = null;
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
		{#if chosenKategorie !== null && chosenKategorie !== undefined}
			<div 
				class="get-back" 
				on:click={() => {
				chosenKategorie = null;
			}}>← Výběr kategorií</div>
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

			{#if strany[strany[chosenStrana].id].kategorie[chosenKategorie]}
				{#each strany[strany[chosenStrana].id]?.kategorie[chosenKategorie] as bp}
					<BodProgramu {bp} barva={strany[chosenStrana].barva} />
				{:else}
					<div id="loading">
						<div class="lds-ring"><div></div><div></div><div></div><div></div></div> Načítání...
					</div>
				{/each}
			{:else}
				<div id="loading">
					V této kategorii nemá strana {strany[chosenStrana].nazev} žádné body programu.
				</div>
			{/if}
		{:else}
			<div class="flex">
				<div>
				</div>
				<div>
					<span>Na co se to vlastně dívám?</span>
					<img src="/QuestionMark.png" alt="Otazník" />
				</div>
			</div>
			<h3 style="margin-top: 10px">Vyber kategorii</h3>
			<div id="kategorie">
				<div 
					class="kat" 
					style="background-color: grey; color: #fff;"
					on:mouseenter={() => {
						if (!isPc) return
						loadData(strany[chosenStrana].id, 0);
					}}
					on:click={() => {
						if (!isPc) {
							loadData(strany[chosenStrana].id, 0);
						}
						chosenKategorie = 0

						console.log(chosenKategorie === undefined)
					}} 
				>
					Všechny
				</div>
				{#each kategorie as kat}
					<div 
						class="kat" 
						style="background-color: {kat.barva}; color: {kat.jmeno == "Energetika" || kat.jmeno == "Stát a vnitro" ? "#2D2D2D" : "#ffffff"}"
						on:mouseenter={() => {
							if (!isPc) return
							loadData(strany[chosenStrana].id, kat.id);
						}}
						on:click={() => {
							if (!isPc) {
								loadData(strany[chosenStrana].id, kat.id);
							}
							chosenKategorie = kat.id
						}} 
					>
						{kat.jmeno}
					</div>
				{/each}
				<div class="boiler"></div>
				<div class="boiler"></div>
				<div class="boiler"></div>
			</div>
		{/if}
	</div>
</section>

<style>
	#kategorie {
		display: flex;
		flex-wrap: wrap;
		justify-content: space-between;
	}
	.kat {
		border-radius: 5px;
		width: 23%;
		padding: 30px 0;
		text-align: center;
		margin-bottom: 20px;
		
		font-family: "Barlow";
		font-weight: 500;
		font-size: 26px;
		
		cursor: pointer;
		transition: .15s;
	}
	.kat:hover {
		filter: brightness(75%);
	}

	.boiler{
		width: 23%;
	}

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

	.get-back{
		font-size: 18px;
		font-weight: 500;
		margin-bottom: 20px;
		display: inline-block;
		padding: 5px 8px;
		border-radius: 3px;

		background-color: #fff;
		color: #000;

		cursor: pointer;
		transition: .15s;
	}
	.get-back:hover{
		filter: brightness(85%);
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

	select::-ms-expand {
		display: none; 
	}

	#loading{
		display: flex;
		align-items: center;
  		justify-content: center;

		font-size: 22px;
		font-weight: 500;
	}
	#loading:nth-child(2) {
		margin-left: 40px;
	}
	.lds-ring {
		display: inline-block;
		position: relative;
		width: 60px;
		height: 60px;
	}
	.lds-ring div {
		box-sizing: border-box;
		display: block;
		position: absolute;
		width: 46px;
		height: 46px;
		margin: 8px;
		border: 8px solid #fff;
		border-radius: 50%;
		animation: lds-ring 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
		border-color: #fff transparent transparent transparent;
	}
	.lds-ring div:nth-child(1) {
		animation-delay: -0.45s;
	}
	.lds-ring div:nth-child(2) {
		animation-delay: -0.3s;
	}
	.lds-ring div:nth-child(3) {
		animation-delay: -0.15s;
	}
	@keyframes lds-ring {
		0% {
			transform: rotate(0deg);
		}
		100% {
			transform: rotate(360deg);
		}
	}
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
		.kat {
			width: 31%;
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
		.kat {
			width: 48%;
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
	@media (max-width: 550px) {
		.kat {
			width: 100%;
			padding: 20px 0;
		}
	}
</style>
