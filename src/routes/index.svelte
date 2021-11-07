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
		kategorie.push({jmeno: "Všechny", id: 0})
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
import IndexContentBp from "../components/IndexContentBP.svelte";

	export let strany = [];
	export let kategorie = [];

	let chosenStrana = "";

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
			return
		};
		
		function compare( a, b ) {
			let collator = new Intl.Collator('cs');
			let order = collator.compare(a.kategorie, b.kategorie);

			if (order === 0) {
				const order2 = collator.compare(a.nadpis, b.nadpis)
				return order2
			} else {
				return order
			}
		}

		json.bps.sort(compare);

		strany[strana_id].kategorie[kategorie_id] = json.bps;
	}

	var isPc = true;
	onMount(() => {
		isPc = window.matchMedia("(hover: hover) and (pointer: fine)").matches
	})

	/*	let showFilters = false;
		let splneno1 = true;
		let splneno2 = true;
		let splneno3 = true;
		let splneno4 = true;

		let filteredBps = null;

		function filterBPs() {
			filteredBps = strany[strany[chosenStrana].id]?.kategorie[chosenKategorie].filter(bp => {
				switch (bp.splneno) {
					case 1:
						return splneno1
					case 2:
						return splneno2
					case 3:
						return splneno3
					case 4:
						return splneno4
				
					default:
						return false
				}
			})
	} */


	// PRO DANA #2
	const menu = [
		{
			nadpis: "O co jde?",
			content: "Slibotechny jsou studentským projektem, který se snaží upozorňovat na plnění a neplnění politických programů. Během voleb mapujeme volební programy politických stran a hnutí a hledáme v nich konkrétní návrhy. Celé následující období pak sledujeme a vyhodnocujeme zda a do jaké míry jsou návrhy prosazovány.",
		},
		{
			nadpis: "Proč je to důležité?",
			content: "Celý web jsme stvořili s těmito záměry:<ol><li>Poskytnout občanům jednoduchý přehled politických programů se zaměřením na konkrétní podporované návrhy.</li><li>Umožnit lidem sledovat do jaké míry politická uskupení svůj předvolební program dodržují. Ostatně, prosazování jejich programů by měl být jeden z hlavních důvodů jejich volby.</li><li>Donutit volební strany a hnutí psát kvalitní, srozumitelné a konkrétní volební programy.</li><li>Připomínat politickým stranám a hnutím jejich volební program během celého jejich mandátu. Nechceme, aby programy vyšuměly týden po volbách.</li><li>A nakonec chceme, aby politické programy hrály při volbách opět podstatnou roli. V době, kdy o výsledku voleb rozhodují lajky na Facebooku je to opravdu třeba. Koneckonců, nastupujete-li do vlaku, taky budete rádi, když mašinfíra po pár kilometrech jízdy nezapomene, s cílem jaké stanice jste vlastně nastoupili do vlaku právě k němu.</li></ol>",
		},
		{
			nadpis: "Jak používat tento web?",
			content: "V horním výberu klikněte na stranu, jež vás zajímá. Následně zvolte kategorii. Objeví se seznam témat z dané kategorie. Po kliknutí na libovolné téma se zobrazí stručné shrnutí postoje strany k danému tématu, konkrétní návrhy, které chce strana prosadit, přesnou citaci z programu a odkaz na originální verzi programu. U každého návrhu pak uvidíte jeho status, odpovídající tomuto klíči:<br><br>✅ Bod byl splněn<br>❌ Bod nebyl splněn<br>❔ Nelze přesvědčivě určit, zda byl bod splněn<br>➖ Bod dosud čeká na splnění<br><br>Tyto značky se pak v průběhu volebního období budou měnit v závislosti na tom, jak daná strana svůj program prosazuje.",
		},
		{
			nadpis: "Jak jsou zpracovány programy stran?",
			content: "Zaměřili jsme se na nejvýraznější politické subjekty, které v roce 2021 v předvolebních průzkumech dokázaly překročit 5% hranici pro vstup do sněmovny. U těchto politických uskupení jsme využili jejich nejdelší verzi programu a rozdělili ji do 16 kategorií. V každé kategorii jsme pak jednotlivé návrhy seskupili do několika témat. Každé téma pak obsahuje několik konkrétních návrhů, které chce daná strana či hnutí prosadit.<br>Obecně jsme se snažili co nejvíce respektovat logické členění programu jednotlivých stran. Konkrétní návrhy jsme se pak snažili vystihnout významově co nejblíže originálnímu znění a myšlence. Ideálně jsme dané návrhy přímo citovali. Naopak u shrnutí tématu jsme usilovali zejména o přehlednost pro čtenáře. Někdy tedy vycházíme z formulací programů, jindy však používáme slova vlastní.",
		},
		{
			nadpis: "Jak se vyhodnocuje plnění programů?",
			content: "Ten nejnáročnější úkol stojí teprve před námi. Je potřeba průběžně sledovat činnost politických uskupení a porovávat ji s jejich programem. V brzké době zveřejníme přesnou metodiku, jak na to půjdeme. Je samozřejmé, že v hodnocení nelze pohlížet stejně na strany vládní a strany opoziční. Nicméně i opozice má ve sněmovně jasně danou roli, a i její plnění by mělo odpovídat volebním programům.",
		},
		{
			nadpis: "Kdo za tím vším stojí?",
			content: "<ul><li>Prokop Válek - student Mendelova gymnázia Opava</li><li>Michal Pavlíček - student Mendelova gymnázia Opava, webmaster</li><li>Zita Maršíková - studentka Mendelova gymnázia Opava</li><li>Veronika Rodáková - studentka Mendelova gymnázia Opava</li><li>Natálie Šebestová - studentka Gymnázia Jaroslava Vrchlického v Klatovech</li><li>Martina Kozlová - studentka Fakulty informatiky a Fakulty filozofie Masarykovy univerzity</li><li>Jakub Neužil - student Fakulty sociálních studií Ostravské univerzity</li><li>Jan Hrazdil - student Fakulty potravinářské a biochemické technologie VŠCHT v Praze</li><li>Adam Klásek - student Ekonomicko-správní fakulty Masarykovy univerzity</li><li>Daniel Rychlý - informatik</li></ul><br>Jistě však uvítáme další pomocnou ruku. Dejte nám vědět!",
		},
		{
			nadpis: "A kontakt?",
			content: "Mail: <a href='mailto:info@slibotechny.cz'>info@slibotechnycz</a><br>Instagram: <a href='https://www.instagram.com/slibotechny/'>@slibotechny</a>",
		}
	]
	// KONEC PRO DANA #2
</script>

<Logo />

<!-- {#if showFilters} 
	<div id="filters">
		<h4>Filtry a řazení</h4>
		<h5>Podle splnění bodu</h5>
		<input type="checkbox" id="splneno1" name="splneno1" value="1" bind:checked={splneno1} on:click={filterBPs}>
		<label for="splneno1">Splněno</label>
		<input type="checkbox" id="splneno2" name="splneno2" value="2" bind:checked={splneno2} on:click={filterBPs}>
		<label for="splneno2">Nesplněno</label>
		<input type="checkbox" id="splneno3" name="splneno3" value="3" bind:checked={splneno3} on:click={filterBPs}>
		<label for="splneno3">Částečně splněno</label>
		<input type="checkbox" id="splneno4" name="splneno4" value="4" bind:checked={splneno4} on:click={filterBPs}>
		<label for="splneno4">Zatím neadresováno</label>
	</div>
{/if} -->

<section>
	<div id="flex">
		{#each strany as strana, index}
			{#if index < 7}
				<div
					class="strana-clickable {index === chosenStrana && 'active'}"
					style="background-color: {strana.barva}; color: {strana.sekundarni_barva};"
					on:click={() => {
						chosenStrana = index;
						/* chosenKategorie = null; */
						if (chosenKategorie || chosenKategorie === 0) {
							loadData(strany[index].id, chosenKategorie)
						}
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
			style="background-color: {chosenStrana !== "" ? strany[chosenStrana]?.barva : "#fff"};
			 	   color: {chosenStrana !== "" ? strany[chosenStrana]?.sekundarni_barva : "#2d2d2d"};
				   border: {chosenStrana !== "" ? "0" : "5px solid #2d2d2d"}
				"
		>	
			{#if chosenStrana === ""}
				<option value="" disabled selected>Vyberte stranu</option>
			{/if}
			{#each strany as strana, index}
				<option
					value={index}
					style="background-color: {strana.barva};
							color: {strana.sekundarni_barva};"
				>
					{strana.nazev}
				</option>
			{/each}
		</select>
	</div>

	<div
		id="body-programu"
		style="background-color: {chosenStrana !== "" ? strany[chosenStrana]?.barva : "#fff"}; 
		color: {chosenStrana !== "" ? strany[chosenStrana]?.sekundarni_barva : "#2d2d2d"};
		border: {chosenStrana !== "" ? "0" : "5px solid #2d2d2d"}
		"
	>
		{#if chosenStrana === ""}



			<!-- SAFE SPACE PRO DANA -->
			<!-- Hledej "PRO DANA #2" -->
			<!-- OLD CONTENT
				<h2 id="main-title" style="text-align:center;">Vítejte na webu Slibotechny.cz!</h2>
				<h3 style="text-align:center;">Co jsou to Slibotechny a proč vznikly?</h3>
				<div id="main-text" style="margin:0 auto; width:70%">
					<p>
					Projevovat občanskou aktivitu se dá mnoha způsoby, velké části lidí však na mysl přijde zejména jeden - volby. Nedávno proběhly volby do Poslanecké sněmovny ČR, ve kterých svůj hlas odevzdalo nejvíce lidí v tomto století. Občané Česka tedy zájem o dění ve své zemi rozhodně mají, zapojení však u voleb zdaleka nemusí končit -  mělo by trvat nejlépe celé volební období. Během tohoto období je třeba zvolené politiky a političky držet odpovědnými za své sliby a programy, které představili ve volbách. Jedná se o nesnadný úkol, který se snaží zjednodušit a zpříjemnit právě web Slibotechny.
					</p>
					<p>
					Slibotechny jsou studentským projektem, který se snaží upozorňovat na plnění a neplnění politických programů a slibů, na které v programech politické strany lákají voliče. Konkrétně jsme se zaměřili na nejvýraznější politické subjekty, které v roce 2021 v předvolebních průzkumech dokázaly překročit 5 % hranici pro vstup do sněmovny. 
					</p>
					<p>
					To znamená celkem 10 politických stran a hnutí (koalice Spolu a Piráti a Starostové jsou rozděleny na jednotlivé strany, jelikož mají v Poslanecké sněmovně samostatné kluby). Terčem našeho zájmu sice budou zejména strany zastoupené ve sněmovně, nezapomínáme však také na některé menší strany se značným volebním potenciálem.
					</p>
					<p>
					U každého politického subjektu se můžete jednoduše přesvědčit, které programové body byly skutečně splněny a které byly jen prázdnými výroky. Body jsou řazeny do různých kategorií a seskupují několik konkrétních návrhů. Jedná se zpravidla o návrhy, u kterých jde co nejobjektivněji potvrdit jejich splnění. Plnění programových bodů a návrhů je rozděleno na 4 kategorie:
					</p>
					<p>
					✅ Bod byl splněn
					</p>
					<p>
					❌ Bod nebyl splněn
					</p>
					<p>
					❔ Nelze přesvědčivě určit, zda byl bod splněn
					</p>
					<p>
					➖ Bod dosud čeká na splnění
					</p>
					<p>
					Během následujícího volebního období budete na webu Slibotechny postupně moct sledovat, které body a návrhy se stranám podařilo splnit a také proč se tomu tak stalo. V budoucnu se můžete těšit například i na blogovou sekci.
					</p>
					<p style="font-weight: bold;">
					Našim cílem je, aby si politici za svými programy skutečně stáli a aby je nepoužívali jen na sbírání hlasů. Budeme rádi, když nám v tom pomůžete.
					</p>
				</div> 
			-->

			<h2 id="main-title" style="text-align:center;">Dohlížíme na plnění politických programů!</h2>
			<div id="main-text" style="margin:0 auto; width:100%">
			</div> 
			<br>
			{#each menu as point}
				<IndexContentBp nadpis={point.nadpis} content={point.content} />
			{/each}


			<!-- KONEC SAFE SPACE PRO DANA  -->

		{:else}
			{#if chosenKategorie !== null && chosenKategorie !== undefined}
				<div class="flex">
					<div 
						class="get-back" 
						on:click={() => {
						chosenKategorie = null;
					}}>← Výběr kategorií</div>
					<!-- <div on:click|self={() => {
						showFilters = !showFilters
					}}>
						<img src="/Filtr.png" alt="Filtr" on:click|self={() => {
							showFilters = !showFilters
						}}/>
						<span on:click|self={() => {
							showFilters = !showFilters
						}}>
							Zobrazit filtry a řazení
						</span>
					</div> -->
					<div on:click={() => {
						chosenStrana = "";
					}}>
						<span>Na co se to vlastně dívám?</span>
						<img src="/QuestionMark.png" alt="Otazník" />
					</div>
				</div>
				<h3>Body programu z kategorie 
					{kategorie.filter(kat => {
						return kat.id === chosenKategorie
					})[0].jmeno}
				</h3>

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
					<h3 style="margin-top: 10px">Vyber kategorii</h3>
					<div on:click={() => {
						chosenStrana = "";
					}}>
						<span>Na co se to vlastně dívám?</span>
						<img src="/QuestionMark.png" alt="Otazník" />
					</div>
				</div>
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
						}} 
					>
						Všechny
					</div>
					{#each kategorie as kat}
						<div 
							class="kat" 
							style="background-color: {kat.barva}; color: {kat.jmeno == "Energetika" || kat.jmeno == "Stát a vnitro" || kat.jmeno == "Ze" ? "#2D2D2D" : "#ffffff"}; {!kat.barva && "display: none;"}"
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
		{/if}
	</div>
</section>

<style>
	/* #filters {
		position: fixed;
		top: 50%;
		left: 50%;
		transform: translateX(-50%) translateY(-50%);

		min-width: 400px;
		padding: 15px 20px;

		background-color:rgba(40, 40, 40, .95);
		color: #fff;

		z-index: 1000;
	}
	#filters h4{
		font-size: 30px;
		font-weight: 500;
		margin: 0;
		margin-bottom: 10px;
	}
	#filters h5{
		font-size: 24px;
		font-weight: 400;
		margin: 0;
		margin-bottom: 5px;
	} */

	#main-title {
		margin: 10px 0;
		margin-bottom: 15px;
		font-size: 40px;
	}
	#main-text {
		font-size: 19px;
		line-height: 135%;
	}
	#main-text p {
		margin: 0 0 10px 0;
	}

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

		.flex {
			flex-direction: column;
		}
		.flex div {
			margin-bottom: 20px;
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
