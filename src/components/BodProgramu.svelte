<script>
	import { afterUpdate, beforeUpdate, onMount } from "svelte";

	export let bp;
	export let barva;

	var box;
	var button;
	var expanded;
	var height;

	var barvaScoped = barva;

	function calculateBoxHeight() {
		box = document.getElementById("box" + bp.id);
		button = document.getElementById("test" + bp.id);
		expanded = false;

		box.style.height = "auto";
		height = box.offsetHeight;
		box.style.height = "0px";

		button.addEventListener("click", letsGo);
	}

	function letsGo(e) {
		var nadpis = document.getElementById("nadpis" + bp.id);
		var sipka = document.getElementById("sipka" + bp.id);
		var sipkai = document.getElementById("sipkai" + bp.id);
		if (
			button != e.target &&
			nadpis != e.target &&
			sipka != e.target &&
			sipkai != e.target
		)
			return;
		if (expanded) {
			box.style.height = 0;
			expanded = false;
		} else {
			box.style.height = height + "px";
			expanded = true;
		}
	}

	afterUpdate(() => {
		setTimeout(() => {
			if (barvaScoped != barva) {
				button.removeEventListener("click", letsGo);

				calculateBoxHeight();
				barvaScoped = barva;
			}
		}, 10);
	});

	onMount(() => {
		calculateBoxHeight();
	});
</script>

<div class="flex-bp">
	<div class="kategorie" style="background-color: {bp.kategorie_barva};" />
	<div
		class="bod-programu"
		id="test{bp.id}"
		on:click|self={() => {
			bp.otevrit = !bp.otevrit;
		}}
	>
		<span
			class="bp-nadpis"
			id={"nadpis" + bp.id}
			on:click|self={() => {
				bp.otevrit = !bp.otevrit;
			}}>{bp.nadpis}</span
		>
		<div
			class="hidden-bp"
			id="box{bp.id}"
			on:click={() => {
				console.log("Nothing");
			}}
		>
			<p class="kategorie-jmeno" style="color: {bp.kategorie_barva};">
				{bp.kategorie}
			</p>

			{#if bp.argumentace}
				<h4 style="color: {barva};">Shrnutí</h4>
				<p class="argumentace">{@html bp.argumentace}</p>
			{/if}

			{#if bp.navrhy.length > 0}
				<h4 style="color: {barva};">Návrhy k provedení bodu</h4>
				{#each bp.navrhy as navrh}
					<div class="navrh-div">
						<img
							src={"small_" + navrh.splneno + ".png"}
							alt="Splněno"
							class="splneno-img"
						/>
						{navrh.text}
					</div>
				{/each}
			{/if}

			{#if bp.citace}
				<h4 style="color: {barva};">Citace z programu</h4>
				<i class="citace">{@html bp.citace}</i>
			{/if}
			<br />
			{#if bp.odkaz}
				<a
					href={bp.odkaz}
					style="color: {barva};"
					target="_blank"
					class="odkaz"
				>
					Odkaz na program<img src="odkaz.png" alt="Odkaz" />
				</a>
			{/if}
		</div>
		<div
			id="sipka{bp.id}"
			class="sipka-dolu"
			on:click={() => {
				bp.otevrit = !bp.otevrit;
			}}
		>
			<img
				src="sipka-dolu.png"
				alt="Šipka dolů"
				class="sipka-dolu {bp.otevrit && 'rotate'}"
				id="sipkai{bp.id}"
			/>
		</div>
	</div>
	{#if bp.splneno == 1}
		<div class="splneno" style="background-color: rgb(76, 175, 80);">
			<img src={"/tick.png"} alt="Fajfka" />
		</div>
	{:else if bp.splneno == 2}
		<div class="splneno" style="background-color: rgb(223, 71, 89);">
			<img src={"/cross.png"} alt="Křížek" />
		</div>
	{:else if bp.splneno == 3}
		<div class="splneno" style="background-color: rgb(255, 193, 7);">
			<img src={"/qm.png"} alt="Otazník" />
		</div>
	{:else if bp.splneno == 4}
		<div class="splneno" style="background-color: rgb(130, 136, 144);">
			<img src={"/-.png"} alt="Pomlčka" />
		</div>
	{/if}
</div>

<style>
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
	h4 {
		font-size: 20px;
		font-weight: bold;
		margin-bottom: 5px;
	}
	.bp-nadpis {
		line-height: 40px;
	}
	.hidden-bp {
		cursor: auto;
		overflow: hidden;
		transition: 0.4s;

		font-weight: 400;
	}
	.hidden-bp h4 {
		margin-bottom: 8px;
	}
	.kategorie-jmeno {
		margin-top: 0px;
		font-size: 15px;
	}
	.argumentace {
		margin-top: 0px;
	}

	.kategorie {
		min-width: 10px;
		height: 40px;
	}
	.splneno {
		min-width: 60px;
		height: 40px;
		position: relative;
	}
	.splneno img {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translateY(-50%) translateX(-50%);
	}
	.navrh-div {
		margin-bottom: 10px;
	}
	.splneno-img {
		margin-right: 5px;
	}
	.sipka-dolu {
		position: absolute;
		right: 5px;
		top: 8px;
	}
	.sipka-dolu {
		transform: rotate(0);
		transition: 0.4s;
	}
	.rotate {
		transform: rotate(180deg);
	}

	.citace {
		font-size: 18px;
	}
	.odkaz {
		float: right;
		margin-top: 10px;
		font-size: 17px;
		font-weight: 700;
		text-decoration: underline;

		margin-bottom: 10px;
	}
	.odkaz img {
		vertical-align: middle;
		text-decoration: none;
		margin-left: 5px;
	}

	@media (max-width: 800px) {
		.bp-nadpis {
			font-size: 20px;
		}
		.bod-programu {
			padding: 0 5px;
		}
		.splneno {
			min-width: 40px;
		}
		.splneno img {
			max-width: 20px;
		}

		h4 {
			font-size: 18px;
		}
		.hidden-bp p {
			font-size: 17px;
		}
		.navrh-div {
			font-size: 17px;
		}
		i {
			font-size: 17px;
		}
	}
	@media (max-width: 700px) {
		.bp-nadpis {
			font-size: 16px;
			font-weight: 700;
		}
		.sipka-dolu {
			display: none;
		}
	}
	@media (max-width: 550px) {
		.bp-nadpis {
			font-size: 14px;
		}
	}
</style>
