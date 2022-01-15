<script context="module">
	export async function preload(page, session) {
		const res = await this.fetch(
			"https://slibotechnyapi.pythonanywhere.com/get_clanky",
			{
				method: "GET",
				headers: {
					"content-type": "application/json",
				},
			}
		);
		const json = await res.json();
		let clanky = json.clanek;

		return { clanky };
	}
</script>

<script>
	export let clanky = [];
</script>

<section>
	<h1 id="logo-h1">Slibotechnický blog</h1>
	<div id="logo-podnadpis">
		<h3>Kde komentujeme politickou situaci</h3>
	</div>

	{#each clanky as clanek}
		<article
			on:click={() => {
				window.location.href = "/blog/" + clanek.id.toString();
			}}
		>
			<div class="obrazek">
				<img
					src={clanek.obrazek}
					alt={clanek.nadpis + " Obrázek"}
					width="250"
					height="250"
				/>
				<img
					src="wireframe.png"
					alt="Dekorace hlavního obrázku"
					class="dekorace"
				/>
			</div>
			<div class="info">
				<p class="autor">{clanek.autor}</p>
				<h1 class="nadpis">{clanek.nadpis}</h1>
				<p class="podnadpis">{clanek.podtitulek}</p>
				<p class="datum">{clanek.datum_vytvoreni}</p>
			</div>
		</article>
	{/each}

	<div
		id="get-back"
		on:click={() => {
			window.location.href = "/";
		}}
	>
		&lt;- Slibotechny.cz
	</div>
</section>

<style>
	section {
		background-color: #ffffff;
		width: 80%;
		margin-left: 50%;
		transform: translateX(-50%);
		padding-bottom: 1px;
	}

	article {
		margin: 0px calc((100% - 80rem) / 2);
		display: flex;
		font-family: "Spectral";
		margin-bottom: 100px;
		position: relative;
		cursor: pointer;
	}

	article:hover .nadpis {
		text-decoration: underline;
	}

	.obrazek {
		position: relative;
		margin-right: 70px;
		margin-bottom: 20px;
	}
	.dekorace {
		position: absolute;
		left: 18px;
		top: 17px;
	}

	.info {
		position: relative;
	}
	.autor {
		font-family: "Spectral";
		font-weight: 500;
		font-size: 20px;
		color: #da3334;
	}
	.nadpis {
		font-size: 40px;
		margin-top: -5px;
		margin-bottom: 15px;
	}
	.podnadpis {
		font-size: 22px;
		color: #000;
		line-height: 140%;
	}
	.datum {
		position: absolute;
		left: 0;
		bottom: 0;
		font-size: 22px;
		font-style: italic;
	}

	#logo-h1 {
		padding: 0;
		margin: 0;
		padding-top: 20px;
		font-family: "Spectral";
		text-align: center;
		font-size: 80px;
		font-weight: 700;
		color: #2d2d2d;
		line-height: 100%;
	}
	#logo-podnadpis {
		text-align: center;
		background-color: #ffffff;
		border-radius: 5px;
		display: inline-block;

		display: table;
		margin: 0 auto;
		margin-bottom: 100px;
	}
	h3 {
		font-family: "Spectral";
		font-weight: 500;
		font-size: 28px;
		margin: 0;
		padding: 0;
	}

	#get-back:hover {
		text-decoration: underline;
	}
	#get-back {
		position: fixed;
		top: 20px;
		left: 30px;

		font-size: 22px;
		font-weight: bold;

		cursor: pointer;
	}

	@media (max-width: 1700px) {
		article {
			margin: 35px 30px;
		}
	}
	@media (max-width: 1500px) {
		.autor {
			font-size: 18px;
		}
		.nadpis {
			font-size: 35px;
		}
		.podnadpis {
			font-size: 20px;
		}
		.datum {
			font-size: 20px;
		}
	}
	@media (max-width: 1450px) {
		#logo-h1 {
			padding-top: 60px;
		}
	}
	@media (max-width: 1400px) {
		.autor {
			font-size: 16px;
		}
		.nadpis {
			font-size: 32px;
		}
		.podnadpis {
			font-size: 18px;
		}
		.datum {
			font-size: 18px;
		}
	}
	@media (max-width: 1300px) {
		.nadpis {
			font-size: 28px;
		}
		h3 {
			font-size: 24px;
		}
	}
	@media (max-width: 1200px) {
		#logo-podnadpis {
			margin-bottom: 30px;
		}
		section {
			width: 90%;
		}
		article {
			flex-direction: column;
		}
		.obrazek {
			margin-left: 50%;
			transform: translateX(-50%);
		}
		.info {
			margin-top: 20px;
			padding-bottom: 30px;
		}
		h3 {
			font-size: 22px;
		}
	}
	@media (max-width: 700px) {
		article {
			flex-direction: column;
		}
		.obrazek {
			margin-left: 0;
			transform: translateX(0);
		}
		.info {
			margin-top: 20px;
			padding-bottom: 30px;
		}
		.nadpis {
			font-size: 26px;
			line-height: 26px;
			margin-top: 10px;
		}
		h3 {
			font-size: 20px;
		}
		#logo-h1 {
			font-size: 60px;
		}

		#get-back {
			left: 10px;
		}
	}
	@media (max-width: 500px) {
		#logo-h1 {
			font-size: 45px;
		}
		h3 {
			font-size: 17px;
		}
	}
	@media (max-width: 400px) {
		#logo-h1 {
			font-size: 35px;
		}
		h3 {
			font-size: 12px;
		}
	}
</style>
