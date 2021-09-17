<script>
	import '../public/global.css'
	import {onMount} from 'svelte'
	const reactVersion = '16.7.0'
	const targets = [
		{
			name: 'Skypack',
			label: 'SP',
			urls: {
				reactUrl: 'https://cdn.skypack.dev/react?min',
				jqueryUrl: 'https://cdn.skypack.dev/jquery?min'
			}
		},
		{
			name: 'JSDelivr',
			label: 'JSD',
			urls: {
				reactUrl: 'https://cdn.jsdelivr.net/npm/react@16.7.0/umd/react.production.min.js',
				jqueryUrl: 'https://cdn.jsdelivr.net/npm/jquery@3.2.1/dist/jquery.min.js'
			}
		},
		{
			name: 'UNPKG',
			label: 'UPG',
			urls: {
				reactUrl: 'https://unpkg.com/react@16.7.0/umd/react.production.min.js',
				jqueryUrl: 'https://unpkg.com/jquery@3.2.1/dist/jquery.min.js'
			}
		},
		{
			name: 'cdnjs',
			label: 'cjs',
			urls: {
				reactUrl: 'https://cdnjs.cloudflare.com/ajax/libs/react/16.7.0/umd/react.production.min.js',
				jqueryUrl: 'https://cdnjs.cloudflare.com/ajax/libs/jquery/3.2.1/jquery.min.js'
			}
		},
		{
			name: 'Netlify',
			label: 'ts',
			urls: {
				reactUrl: '/downloadTests/react.production.min.js',
				jqueryUrl: '/downloadTests/jquery.min.js'
			}
		}
	]
	async function render(url) {
		try {
			const loadUrl = fetch(url, {cache: "no-store"});
			const start = performance.now()
			const load = await loadUrl
			const end = performance.now() - start
			return end
		} catch (err) {
			console.warn(err)
			return Infinity
		}
	}
	var itemsBack = 0
	var results = []
	var dataDone = false
	async function scanItems(element) {
		console.log('scanItems')
		var reactResult = await render(element.urls.reactUrl)
		var jqueryResult = await render(element.urls.jqueryUrl)
		results.push({
			reactResult: reactResult.toFixed(1),
			jqueryResult: jqueryResult.toFixed(1),
			name: element.name
		})
		itemsBack++
		if (itemsBack == targets.length) {
			dataDone = true
			itemsBack = 0
			console.log('set is last')
		}
	}
	function startScan() {
		dataDone = false
		results = []

		var itemsSent = 0
		targets.forEach(element => {
			itemsSent++
			scanItems(element)
		});
	}
	onMount(()=>{
		startScan()
		setInterval(() => {
			startScan()
		}, 40000);
	})

</script>
<div class="flexwrapper">
	<div class="centerwrapper">
		<div class="hero">
			<h1>Please don't use public CDNs.</h1>
			<h3 class="subtitle">Public CDNs are insecure and can lead to security <a href="https://blog.ryotak.me/post/cdnjs-remote-code-execution-en/" rel="none" target="_blank">vulnerabilities</a> and developer <a href="https://httptoolkit.tech/blog/public-cdn-risks/" rel="none" target="_blank">headaches</a>. <br>These days, it's faster to load of services like <a href="https://vercel.com" target="_blank">Vercel</a> or <a href="https://www.netlify.com" target="_blank">Netlify</a>, not to mention vastly more secure.</h3>
		</div>
		<main>
			<button on:click={startScan}>Run Again</button>
			<div class="resultwrapper">			
				{#if dataDone}
						<ul>
							{#each results as {reactResult, jqueryResult, name}}
								<li class="resultitem">
									<h3>{name}</h3>
									<h4>Download Speed:</h4>
									<div class="filenamesection">
										<span class="filenametitle">React:</span>
										<span class="filenameresult">{reactResult}ms</span>
									</div>
									<div class="filenamesection">
										<span class="filenametitle">Jquery:</span>
										<span class="filenameresult">{jqueryResult}ms</span>
									</div>
								</li>
							{/each}
						</ul>
						{:else}
						Loading Data...
						{/if}
			</div>
			<p class="smalltext">Please don't use public CDNs. Is not related or affiliated with any mentioned entity, company, writer, website or article featured in this site.</p>
			<p>A project by <a href="https://nomical-labs.com?ref=publiccdn" target="_blank">nomical labs.</a></p>
		</main>	
	</div>
</div>
