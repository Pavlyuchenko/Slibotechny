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

	onMount(() => {
		setTimeout(getAllData, 1);
	});

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

	/* async function getData() {
		cookieStorage = localStorage.getItem("user_cookie");
		prezdivkaStorage = localStorage.getItem("prezdivka");
		const res = await fetch(
			"https://velkadomu.pythonanywhere.com/clanek/" + id,
			{
				method: "POST",
				headers: {
					"content-type": "application/json",
				},
				body: JSON.stringify({
					prezdivka: prezdivkaStorage,
					cookie: cookieStorage,
				}),
			}
		);
		const json = await res.json();
		clanek = json.clanek;
		komentare = json.komentare;
		dalsiClanky = json.dalsiClanky;
		url =
			"https://ik.imagekit.io/velkadomu/tr:h-500,w-900" + clanek.obrazek;
		jsonld = {
			"@context": "https://schema.org",
			"@type": "NewsArticle",
			headline: clanek.titulek,
			image: [
				"https://ik.imagekit.io/velkadomu/tr:h-500,w-900" +
					clanek.obrazek,
			],
			datePublished: clanek.datePublished,
		};
		jsonld = JSON.stringify(jsonld);
		jsonldScript = `<script type="application/ld+json">${
			jsonld + "<"
		}/script>`;
		nazevAnkety = clanek.nazevAnkety;
		votes = clanek.votes;
		hodnotyAnkety = clanek.hodnotyAnkety;
		userChosen = json.user_chosen;
		if (userChosen) {
			showAnketaResults = true;
		}
		statsIncrease();
	} */
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

	<div
		id="body-programu"
		style="background-color: {strany[chosenStrana].barva}; color: {strany[
			chosenStrana
		].sekundarni_barva};"
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
				<div class="flex-bp">
					<div
						class="kategorie"
						style="background-color: {bp.kategorie_barva};"
					/>
					<div
						class="bod-programu"
						on:click|self={() => {
							bp.otevrit = !bp.otevrit;
						}}
					>
						<span
							class="bp-nadpis"
							on:click|self={() => {
								bp.otevrit = !bp.otevrit;
							}}>{bp.nadpis}</span
						>
						<div
							class="hidden-bp"
							style="height: {bp.otevrit ? '500px' : '0px'};"
							on:click={() => {
								console.log("Nothing");
							}}
						>
							<p
								class="kategorie-jmeno"
								style="color: {bp.kategorie_barva};"
							>
								{bp.kategorie}
							</p>
							<p class="argumentace">{@html bp.argumentace}</p>
							{#if bp.citace}
								<p class="citace">{@html bp.citace}</p>
							{/if}
						</div>
						<div
							id="sipka-dolu"
							on:click={() => {
								bp.otevrit = !bp.otevrit;
							}}
						>
							<img src="sipka-dolu.png" alt="Šipka dolů" />
						</div>
					</div>
					<div
						class="splneno"
						style="background-color: {bp.splneno == 1
							? 'rgb(76, 175, 80)'
							: bp.splneno == 2
							? 'rgb(223, 71, 89)'
							: bp.splneno == 3
							? 'rgb(255, 193, 7)'
							: 'rgb(130, 136, 144)'};"
					/>
				</div>
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
		height: 650px;
		border-radius: 5px;
		padding: 20px 20px;
		box-sizing: border-box;
		transition: 0.15s;
		overflow-y: auto;
		overflow-x: hidden;
	}

	.flex-bp {
		display: flex;
		justify-content: space-between;
		margin-bottom: 20px;
	}

	.bod-programu {
		width: 100%;
		min-height: 40px;
		background-color: #ffffff;
		color: #000000;
		box-sizing: border-box;

		padding: 0px 12px;

		font-size: 22px;
		font-weight: 500;

		position: relative;

		transition: 0.4s;
		cursor: pointer;
	}
	.bp-nadpis {
		line-height: 40px;
	}
	.hidden-bp {
		cursor: auto;
		overflow: hidden;
		transition: 0.4s;
	}
	.kategorie-jmeno {
		margin-top: 0px;
		font-size: 15px;
	}
	.argumentace {
		margin-top: 15px;
	}

	.kategorie {
		min-width: 10px;
		height: 40px;
	}
	.splneno {
		min-width: 60px;
		height: 40px;
	}
	#sipka-dolu {
		position: absolute;
		right: 10px;
		top: 8px;
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

	@media (max-width: 1500px) {
		section {
			padding: 0 30px;
		}
	}
</style>
