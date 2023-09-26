<script>
	import { page } from '$app/stores';
</script>

<header>
	<nav>
		<ul>
			<li aria-current={$page.url.pathname === '/WormFarm' ? 'page' : undefined}>
				<a href="/WormFarm">Worm Farm</a>
			</li>
			<li aria-current={$page.url.pathname === '/GlowWormFarm' ? 'page' : undefined}>
				<a href="/GlowWormFarm">Glow Worm Farm</a>
			</li>
			<li aria-current={$page.url.pathname === '/SeedCollector' ? 'page' : undefined}>
				<a href="/SeedCollector">Seed Collector</a>
			</li>
			<li aria-current={$page.url.pathname === '/PreservesJar' ? 'page' : undefined}>
				<a href="/PreservesJar">Preserves Jar</a>
			</li>
			<li aria-current={$page.url.pathname === '/about' ? 'page' : undefined}>
				<a href="/about">About</a>
			</li>
		</ul>
	</nav>
</header>

<style>
</style>
