<script>
	let rResult, gResult, bResult
	let guess = [
		{color:"red",value:120},
		{color:"green",value:120},
		{color:"blue",value:120}
	]

	let score = 0, level = 1
	
	let selected = false
	let open = false
	let loading = false
	let result = false
	let finish = false
	let r = ""

	let tapedTwice = false;

	function keydown(e) {
		console.log(e)
		if(e.keyCode == 123) {
			e.stopPropagation()
			e.preventDefault()
		return false;
		}
		if(e.ctrlKey && e.shiftKey && e.keyCode == 'I'.charCodeAt(0)){
			e.stopPropagation()
			e.preventDefault()
		return false;
		}
		if(e.ctrlKey && e.shiftKey && e.keyCode == 'J'.charCodeAt(0)){
			e.stopPropagation()
			e.preventDefault()
			return false;
		}
		if(e.ctrlKey && e.keyCode == 'U'.charCodeAt(0)){
			e.stopPropagation()
			e.preventDefault()
		return false;
		}
	}

	function tapHandler(event) {
		if(!tapedTwice) {
			tapedTwice = true;
			setTimeout( function() { tapedTwice = false; }, 300 );
			return false;
		}
		event.preventDefault();
		//action on double tap goes below
		openWindow()
	}
	
	function select() {
		selected = !selected
	}

	function openWindow() {
		selected = true
		loading = true
		setTimeout(() => {
			selected = false
			open = true
			loading = false
		}, 800)
	}

	function reset(e) {
		if(e.target.nodeName == "SECTION") {
			selected = false
		}
	}

	function checkResult() {
		if (guess[0].value === rResult && guess[1].value === gResult && guess[2].value === bResult) {
			r="CORRECT"
			score+=100
		} 
		else if ( guess[0].value >= rResult-10 && guess[0].value <= rResult+10 
							 && guess[1].value >= gResult-10 && guess[1].value <= gResult+10 
							&& guess[2].value >= bResult-10 && guess[2].value <= bResult+10 ) {
			r="ALMOST"
			score += 50
		}
		else if ( guess[0].value >= rResult-25 && guess[0].value <= rResult+25
							 && guess[1].value >= gResult-25 && guess[1].value <= gResult+25 
							&& guess[2].value >= bResult-25 && guess[2].value <= bResult+25 ) {
			r="QUITE THERE"
			score+=20
		}
		else { 
			r="WRONG"
		}
		result = true
		if (level == 5) {
			finish = true
		}
	}

	function restartGame() {
		score = 0
		level = 1
		finish = false
		initRandom()
	}

	function initRandom() {
		rResult=Math.round(Math.random()*255)
		gResult=Math.round(Math.random()*255)
		bResult=Math.round(Math.random()*255)
		guess = [
			{color:"red",value:120},
			{color:"green",value:120},
			{color:"blue",value:120}
		]
	}
	initRandom()
</script>

<svelte:head>
	<link rel="preconnect" href="https://fonts.googleapis.com">
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
	<link href="https://fonts.googleapis.com/css2?family=Electrolize&display=swap" rel="stylesheet">
</svelte:head>

<svelte:window on:keydown={keydown}></svelte:window>
<svelte:body on:contextmenu={(e) => {
	e.stopPropagation();
	e.preventDefault();
return false
}} />

<section on:click={reset}>
	<div class="icon" on:click={select} on:dblclick={openWindow} style:pointer-events="auto" on:touchstart={tapHandler}>
		<div class="overlay" style:opacity={selected ? 1 : 0}></div>
	<img alt="logo" src="/images/rgb_logo.png"/>	
	<p style:white-space="normal">rgb.exe</p>
	</div>
	<div class="open loading"
		style:pointer-events="none"
		style:position="absolute"
		style:display="flex"
		style:justify-content="center"
		style:align-items="center"
		class:opened={loading}>
		<img src="/images/hourglass_icon.png" alt="loading">
	</div>
	<div class="open" style="position:absolute">
		<div class="main">
		<div class="container"
			class:opened={open}>
			<div class="inner">
				<div class="window shadow"></div>
			<div class="window">
				<div class="titlebar">
					<p>GuessTheRGB</p>
				</div>
			<div class="content" style:flex-direction="column">
				<div style:display="flex" style:justify-content="space-between" style:width="90%"><p>level: {level}</p><p>score: {score}</p></div> 
				<div class="resultColor" style:background-color="rgb({rResult}, {gResult}, {bResult})">
					<!-- <h1 style:color="white">{rResult}, {gResult}, {bResult}</h1> -->
					<div class="check" on:click={checkResult} style:pointer-events={open ? "auto" : "none"}>Check Result</div>
				</div>
		</div>
	</div>
			</div>
		
		</div>
		</div>

		<div class="colors">
		{#each guess as g, i}
		<div 
			class="container" 
			class:opened={open}
			style:transition-delay="{i*200+500}ms"
			style:top="{Math.random()*50}px"
			>
			<div class="flex">
			<div class="window single red shadow"></div>
			<div class="window single red">
				<div class="titlebar">
					<p>{g.color ?? g.color}</p>
				</div>
				<div class="content">
					<div class="baseColor">
			<div 
				style:background-color="rgb({i == 0 ? guess[0].value : 0}, {i  == 1 ? guess[1].value : 0}, {i == 2 ? guess[2].value : 0})"
				style:width="100%"
				style:height="100%"
				>
					<div class="button noselect" on:click={()=> guess[i].value--} style:pointer-events={open ? "auto" : "none"}>-</div>
					<input class="guess" type="number" bind:value={guess[i].value} min="0" max="255">
					<div class="button noselect" on:click={()=> guess[i].value++} style:pointer-events={open ? "auto" : "none"}>+</div>
			</div>
			<input type="range" min="0" max="255" bind:value={guess[i].value} style:pointer-events={open ? "auto" : "none"}>
	</div>
				</div>
			</div>
		</div>
			</div>
		{/each}
		</div>
	</div>
	<div class="open loading"
		style:pointer-events={result ? "auto" : "none"}
		style:position="absolute"
		style:display="flex"
		style:justify-content="center"
		style:align-items="center"
		class:opened={result}>
		<div class="window" style:height="150px">
				<div class="titlebar">
					<p>Result</p>
				</div>
				<div class="content"
					style:flex-direction="column">
					{#if finish}
						<p>{r}</p>
						<p>Your Final Score: {score}</p>
						<div
						style:pointer-events={result ? "auto" : "none"} 
						class="check" on:click={()=> {
							restartGame()
							result=false
						}}>Restart Game</div>
					{:else}
						<p>{r}</p>
						<p>Your Score: {score}</p>
						<div 
						style:pointer-events={result ? "auto" : "none"} class="check" on:click={()=> {
						result = false
						level++
						initRandom()
					}}>Next</div>
					{/if}
										
				</div>
		</div>
	</div>
	
</section>

<style>
	* {
		font-family:'Electrolize',sans-serif;
		font-weight:600;
		color:var(--dark-color);
	}
	:root {
		--light-color:#f0eadd;
		--dark-color:#32302d;
		--border-width:3px;
	}
	:global(body, html) {
		margin:0;
		padding:0;
		overflow:hidden;
	}
	section {
		width:100vw;
		height:100vh;
		background-color:#85d9d9;
		background-image: 
			linear-gradient(#ece2cd .1em, transparent .1em),
			linear-gradient(90deg, #ece2cd .1em, transparent .1em);
		background-size: 3em 3em;
		position:absolute;
		display:flex;
		justify-content:center;
	}
	.open {
		pointer-events:none;
		width:88%;
		height:100%;
		max-width:400px;
		display:flex;
		flex-direction:column;
		justify-content: center;
		align-items: center;
	}
	.main {
		height:100%;
	}
	.colors {
		height:100%;
	}
	.loading {
		opacity:0;
	}
	.container {
		opacity:0;
		top:50px;
		position:relative;
		width:100%;
		height:10px;
		display:flex;
	}
	.colors {
		display:flex;
		width:100%;
		justify-content:space-around;
	}
	.colors > .container {
		display:flex;
	}
	.inner {
		width:310px;
		margin:0 auto;
		position:relative;
	}
	.flex {
		position:relative;
		width:110px;
		margin:0 auto;
	}
	.window {
		width:300px;
		height:300px;
		background-color:var(--light-color);
		position:absolute;
		border:solid var(--border-width) var(--dark-color);
		border-radius:0.5rem;
		padding:0;
	}
	.check {
		cursor:pointer;
		background-color:var(--light-color);
		border:solid var(--border-width) var(--dark-color);
		border-radius:0.5rem;
		padding:0.5rem;
	}
	.shadow {
		background-color:var(--dark-color);
		position:absolute;
		top:10px;
		left:10px;
	}
	.single {
		width:100px;
		height:140px;
	}
	.titlebar {
		width:100%;
		border-bottom:solid var(--border-width) var(--dark-color);
		background-color:#dbf3f3;
		border-top-left-radius:0.5rem;
		border-top-right-radius:0.5rem;
		margin-top:0;
	}
	.titlebar > p {
		margin:0;
		text-align: center;
	}
	.content {
		display:flex;
		justify-content:center;
		align-items:center;
		height:90%;
	}
	.single > .content {
		height:90px
	}
	.resultColor {
		width:90%;
		height:90%;
		border: solid var(--border-width) var(--dark-color);
		display:flex;
		align-items:center;
		justify-content:center;
	}
	.baseColor {
		height:60px;
		width:60px;
	}
	.baseColor > div {
		display:flex;
		justify-content:center;
		align-items:center;
		border:solid var(--border-width) var(--dark-color)
	}
	input[type=range] {
		width:100%;
		-webkit-appearance: none;
	  appearance: none;
	  background: transparent;
	  cursor: pointer;
	}
	h1 {
		margin:0;
		color:white;
		mix-blend-mode: luminosity;
	}
	.button {
		background-color:var(--light-color);
		color:var(--dark-color);
		border:solid var(--border-width) var(--dark-color);
		width:20px;
		height:20px;
		aspect-ratio:1;
		border-radius:50%;
		padding:0;
		display:flex;
		justify-content:center;
		align-items: center;
		cursor:pointer;
	}
	.guess {
		background-color:transparent;
		color:white;
		text-align:center;
		border:none;
		font-size:0.8rem;
		font-weight:bold;
		width:25px;
	}

	.guess::-webkit-inner-spin-button, 
	.guess::-webkit-outer-spin-button {  
	   -webkit-appearance:none;
	}

	.noselect {
  -webkit-touch-callout: none; /* iOS Safari */
    -webkit-user-select: none; /* Safari */
     -khtml-user-select: none; /* Konqueror HTML */
       -moz-user-select: none; /* Old versions of Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
            user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome, Edge, Opera and Firefox */}
	
/***** Track Styles *****/
/***** Chrome, Safari, Opera, and Edge Chromium *****/
input[type="range"]::-webkit-slider-runnable-track {
  background: #312e2b;
  height: 0.5rem;
	border-radius:0.25rem;
}

/******** Firefox ********/
input[type="range"]::-moz-range-track {
  background: #312e2b;
  height: 0.5rem;
	border-radius:0.25rem;
}
	/***** Thumb Styles *****/
/***** Chrome, Safari, Opera, and Edge Chromium *****/
input[type="range"]::-webkit-slider-thumb {
   -webkit-appearance: none; /* Override default look */
   appearance: none;
	border: solid 3px #312e2b;
		border-radius: 50%;
   margin-top: -0.25rem; /* Centers thumb on the track */
   background-color: #f0eadd;
   height: 1rem;
   width: 1rem;    
}
	/***** Thumb Styles *****/
/***** Firefox *****/
input[type="range"]::-moz-range-thumb {
    border: solid 3px #312e2b;
		border-radius: 50%;
   margin-top: -0.25rem; /* Centers thumb on the track */
   background-color: #f0eadd;
    height: 1rem;
    width: 1rem;
}
	.opened {
		opacity: 1;
		transition:opacity 200ms ease-in-out;
	}
	img {
		width:40px;
		height:40px;
		object-fit:contain;
	}
	.icon {
		cursor:pointer;
		position:absolute;
		top:50px;
		left:50px;
		width:60px;
		height:60px;
		display:flex;
		flex-direction:column;
		justify-content:center;
		align-items:center;
	}
	p {
		margin:0;
		text-align:center;
		font-weight:600;
	}
	.overlay {
		pointer-events:none;
		width:100%;
		height:100%;
		background-color:rgba(0,0,255,0.2);
		position:absolute;
		top:0;
		left:0;
	}
	
</style>
