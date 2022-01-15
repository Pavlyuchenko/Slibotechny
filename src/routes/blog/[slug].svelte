<script context="module">
	export async function preload(page, session) {
		const { slug } = page.params;
		const res = await this.fetch(
			"https://slibotechnyapi.pythonanywhere.com/get_clanek/" + slug,
			{
				method: "GET",
				headers: {
					"content-type": "application/json",
				},
			}
		);
		const json = await res.json();
		let clanek = json.clanek;

		return { clanek };
	}
</script>

<script>
	export let clanek = [];
</script>

<article>
	<div id="article">
		<div id="obrazek">
			<img src={clanek.obrazek} alt={clanek.nadpis + " Obrázek"} />
			<img
				src="wireframe.png"
				alt="Dekorace hlavního obrázku"
				id="dekorace"
			/>
		</div>
		<div id="info">
			<p id="autor">{clanek.autor}</p>
			<h1 id="nadpis">{clanek.nadpis}</h1>
			<p id="datum">{clanek.datum_vytvoreni}</p>
			<p id="podnadpis">{clanek.podtitulek}</p>
			<p id="text">{@html clanek.text}</p>
		</div>
	</div>

	<div
		id="get-back"
		on:click={() => {
			window.location.href = "/blog";
		}}
	>
		&lt;- Zpět na hlavní stranu
	</div>
</article>

<style>
	p {
		text-align: justify;
	}

	article {
		padding: 0px calc((100% - 115rem) / 2);
		font-family: "Spectral";
		margin-bottom: 100px;
		position: relative;

		background-color: #fff;
		width: 55%;
		padding-top: 60px;
		padding-bottom: 30px;
		margin-left: 50%;
		transform: translateX(-50%);
	}

	#obrazek {
		position: relative;
		margin-right: 70px;
		margin-bottom: 20px;
		width: 40%;

		margin-left: 50%;
		transform: translateX(-50%);
	}
	#obrazek img {
		width: 100%;
	}
	#dekorace {
		position: absolute;
		left: 46px;
		top: 45px;
	}

	#info {
		position: relative;
	}
	#autor {
		font-family: "Spectral";
		font-weight: 500;
		font-size: 26px;
		color: #da3334;

		text-align: center;
		margin-top: 70px;
	}
	#nadpis {
		font-size: 50px;
		margin-top: -5px;
		margin-bottom: 5px;

		text-align: center;
	}
	#datum {
		font-size: 22px;
		font-style: italic;

		text-align: center;
	}
	#podnadpis {
		font-size: 22px;
		color: #000;
		line-height: 140%;

		margin-top: 50px;
		font-weight: bold;
	}
	#text {
		font-size: 22px;
		color: #000;
		line-height: 140%;

		margin-top: 35px;
	}
	#text::after {
		content: "";
		display: inline-block;

		width: 10px;
		height: 10px;
		background-color: #da3334;
		margin-bottom: 2px;
		margin-left: 6px;
	}

	#get-back:hover {
		text-decoration: underline;
	}
	#get-back {
		position: fixed;
		top: 60px;
		left: 30px;

		font-size: 22px;
		font-weight: bold;

		cursor: pointer;
	}

	@media (max-width: 1900px) {
		article {
			padding: 60px 30px;
		}
	}
	@media (max-width: 1600px) {
		#get-back {
			top: 10px;
		}
		#dekorace {
			left: 36px;
			top: 35px;
		}
	}
	@media (max-width: 1400px) {
		#dekorace {
			left: 37px;
			top: 36px;
		}
		#obrazek {
			width: 50%;
		}
	}
	@media (max-width: 1200px) {
		#dekorace {
			left: 33px;
			top: 32px;
		}
		#obrazek {
			width: 55%;
		}
	}
	@media (max-width: 1000px) {
		#dekorace {
			left: 31px;
			top: 30px;
		}
		#obrazek {
			width: 60%;
		}
		#nadpis {
			font-size: 34px;
			line-height: 34px;
		}
	}
	@media (max-width: 800px) {
		#dekorace {
			left: 29px;
			top: 28px;
		}
		#obrazek {
			width: 70%;
		}
		#nadpis {
			font-size: 34px;
			line-height: 34px;
		}
	}
	@media (max-width: 700px) {
		#dekorace {
			left: 34px;
			top: 33px;
		}
		article {
			width: 80%;
		}
		#autor {
			margin-top: 55px;
			font-size: 24px;
		}
	}
	@media (max-width: 450px) {
		#dekorace {
			left: 24px;
			top: 23px;
		}
		#nadpis {
			font-size: 30px;
		}
		#text,
		#podnadpis {
			font-size: 18px;
		}
		#autor,
		#datum {
			font-size: 18px;
		}
		#autor {
			margin-bottom: 10px;
		}
		#get-back {
			left: 5px;
		}
		article {
			padding: 60px 10px;
			width: 85%;
		}
	}
</style>
