<script>
	import { afterUpdate, onMount } from "svelte";

	export let nadpis;
	export let content;

	var box;
	var button;
	var expanded;
	var height;

    var otevrit = false;

    var hiddenGuy;


	function calculateBoxHeight() {
		box = document.getElementById("box" + nadpis);
		button = document.getElementById("test" + nadpis);
		expanded = false;

		hiddenGuy.style.height = "auto";
		height = hiddenGuy.offsetHeight;
		hiddenGuy.style.height = "0px";

		button.addEventListener("click", letsGo);
	}

	function letsGo(e) {
        var something = document.getElementById("nadpis" + nadpis);
		var sipka = document.getElementById("sipka" + nadpis);
		var sipkai = document.getElementById("sipkai" + nadpis);
		if (
			button != e.target &&
			something != e.target &&
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

	onMount(() => {
		calculateBoxHeight();
	});
</script>

<div class="flex-bp" style="background-color: #ffffff; border: 3px solid #000">
	<div
		class="bod-programu"
		id="test{nadpis}"
		on:click|self={() => {
			otevrit = !otevrit;
		}}
	>
		<span
			class="bp-nadpis"
			id={"nadpis" + nadpis}
			on:click|self={() => {
				otevrit = !otevrit;
			}}>{nadpis}</span
		>
		<div
			class="hidden-bp"
			id="box{nadpis}"
            bind:this={hiddenGuy}
		>
            <p class="argumentace">{@html content}</p>
            <br>
		</div>
		<div
			class="sipka-dolu"
			on:click={() => {
				otevrit = !otevrit;
			}}
		>
			<img
				src="sipka-dolu.png"
				alt="Šipka dolů"
				class="sipka-dolu {otevrit && 'rotate'}"
				id="sipkai{nadpis}"
			/>
		</div>
	</div>
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
		background-color: #e6e6e6;
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
		cursor: help;
	}
	.splneno:after {
		display: none;
		position: absolute;
		top: 50%;
		left: 50%;
		border: 2px solid #2d2d2d;
		border-radius: 3px;
		background-color: #fff;
		color: #2d2d2d;
		z-index: 10;
		min-width: 200px;
		padding: 3px 5px;
		font-size: 20px;
		font-weight: 500;
	}
	.splneno:hover:after {
		display: block
	}
	.spl1:after {
		content: "Strana tento bod v plném rozsahu splňuje";
	}
	.spl2:after {
		content: "Hlasování strany je v rozporu s tímto slibem";
	}
	.spl3:after {
		content: "Strana tento bod splňuje pouze částečně";
	}
	.spl4:after {
		content: "Strana tento bod zatím nepodpořila";
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

		h4 {
			font-size: 18px;
		}
		.hidden-bp p {
			font-size: 17px;
		}
		.navrh-div {
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
