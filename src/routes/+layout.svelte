<script>
	import { page } from '$app/state';
	import { tick, onDestroy } from 'svelte';
	let { children } = $props();

	let tocItems = $state([]);
	let activeId = $state('');
	let headingEls = [];
	let scrollHandler = null;

	function cleanup() {
		if (scrollHandler) {
			window.removeEventListener('scroll', scrollHandler);
			scrollHandler = null;
		}
	}

	function buildToc() {
		cleanup();

		const main = document.querySelector('main');
		if (!main) {
			tocItems = [];
			return;
		}

		const allHeadings = Array.from(main.querySelectorAll('h2[id], h3[id], h4[id]'));
		if (allHeadings.length === 0) {
			tocItems = [];
			return;
		}

		const minLevel = Math.min(...allHeadings.map((h) => parseInt(h.tagName[1])));
		const maxLevel = allHeadings.length > 12 ? minLevel : minLevel + 1;

		const filtered = allHeadings.filter((h) => parseInt(h.tagName[1]) <= maxLevel);
		headingEls = filtered;

		tocItems = filtered.map((h) => ({
			id: h.id,
			text: h.textContent.replace(/^#\s*/, ''),
			level: parseInt(h.tagName[1])
		}));

		function updateActive() {
			let current = '';
			for (const h of headingEls) {
				if (h.getBoundingClientRect().top <= 100) {
					current = h.id;
				} else {
					break;
				}
			}
			if (current) activeId = current;
		}

		scrollHandler = updateActive;
		updateActive();
		window.addEventListener('scroll', updateActive, { passive: true });
	}

	$effect(() => {
		// Track pathname so this re-runs on navigation
		page.url.pathname;
		// Wait for the new page content to render
		tick().then(() => {
			// Use requestAnimationFrame to ensure DOM is fully painted
			requestAnimationFrame(() => buildToc());
		});
		return cleanup;
	});

	onDestroy(cleanup);
</script>

<div class="site">
	<header>
		<div class="header-inner">
			<a href="/" class="logo">bpf.wtf</a>
			<nav>
				<a href="/" class:active={page.url.pathname === '/'}>Home</a>
				<a href="/sol-0x03-isa" class:active={page.url.pathname === '/sol-0x03-isa'}>0x03: ISA</a>
				<a href="/sol-0x04-syscalls" class:active={page.url.pathname === '/sol-0x04-syscalls'}>0x04: Syscalls</a>
			</nav>
		</div>
	</header>

	<main>
		{@render children()}
	</main>

	{#if tocItems.length > 0}
		<aside class="toc">
			<nav>
				<span class="toc-title">Contents</span>
				{#each tocItems as item}
					<a
						href="#{item.id}"
						class="toc-link"
						class:active={activeId === item.id}
						class:toc-h3={item.level === 3}
						class:toc-h4={item.level === 4}
					>{item.text}</a>
				{/each}
			</nav>
		</aside>
	{/if}

	<footer>
		<div class="footer-inner">
			<div class="disclaimer">
				<p>
					Original content by <strong>Richard Patel</strong>
					(<a href="https://x.com/fd_ripatel" target="_blank" rel="noopener">@fd_ripatel</a>).
					Maintained with permission by <a href="https://blueshift.gg" target="_blank" rel="noopener">Blueshift</a>
					(<a href="https://x.com/blueshift" target="_blank" rel="noopener">@blueshift</a>).
				</p>
			</div>
			<div class="copyright">
				<p>&copy; 2022, Richard Patel &middot; Maintained by <a href="https://blueshift.gg" target="_blank" rel="noopener">Blueshift</a></p>
			</div>
		</div>
	</footer>
</div>

<style>
	:global(*) {
		margin: 0;
		padding: 0;
		box-sizing: border-box;
	}

	:global(html) {
		scroll-behavior: smooth;
	}

	:global(body) {
		font-family: 'Lora', Georgia, serif;
		color: #1a1a2e;
		background: #fafafa;
		line-height: 1.7;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
	}

	:global(a) {
		color: #0e7490;
		text-decoration: none;
		transition: color 0.15s;
	}

	:global(a:hover) {
		color: #155e75;
		text-decoration: underline;
	}

	:global(code) {
		font-family: 'JetBrains Mono', 'Fira Code', monospace;
		font-size: 0.875em;
		background: #e0f7fa;
		padding: 0.15em 0.4em;
		border-radius: 4px;
		color: #164e63;
		border: 1px solid #b2ebf2;
	}

	:global(pre) {
		font-family: 'JetBrains Mono', 'Fira Code', monospace;
		font-size: 0.85rem;
		background: #1a1a2e;
		color: #e2e8f0;
		padding: 1.25rem 1.5rem;
		border-radius: 8px;
		overflow-x: auto;
		line-height: 1.6;
		margin: 1.5rem 0;
	}

	:global(pre code) {
		background: none;
		padding: 0;
		border-radius: 0;
		border: none;
		color: inherit;
		font-size: inherit;
	}

	:global(h1, h2, h3, h4) {
		font-family: 'Inter', system-ui, sans-serif;
		font-weight: 700;
		line-height: 1.3;
		color: #1a1a2e;
		position: relative;
	}

	:global([id]) {
		scroll-margin-top: 80px;
	}

	:global(h2 > a.anchor-wrap),
	:global(h3 > a.anchor-wrap),
	:global(h4 > a.anchor-wrap) {
		color: inherit;
		text-decoration: none;
		display: block;
	}

	:global(h2 > a.anchor-wrap:hover),
	:global(h3 > a.anchor-wrap:hover),
	:global(h4 > a.anchor-wrap:hover) {
		color: #0e7490;
		text-decoration: none;
	}

	:global(.anchor-hash) {
		color: #d1d5db;
		font-weight: 400;
		margin-right: 0.35em;
		transition: color 0.15s;
	}

	:global(a.anchor-wrap:hover .anchor-hash) {
		color: #0e7490;
	}

	/* Prism syntax highlighting - dark theme */
	:global(.token.comment),
	:global(.token.prolog),
	:global(.token.doctype),
	:global(.token.cdata) {
		color: #6b7280;
	}

	:global(.token.punctuation) {
		color: #9ca3af;
	}

	:global(.token.keyword),
	:global(.token.tag),
	:global(.token.boolean),
	:global(.token.constant),
	:global(.token.symbol) {
		color: #67e8f9;
	}

	:global(.token.number) {
		color: #fbbf24;
	}

	:global(.token.string),
	:global(.token.char),
	:global(.token.attr-string),
	:global(.token.builtin) {
		color: #6ee7b7;
	}

	:global(.token.function) {
		color: #93c5fd;
	}

	:global(.token.operator),
	:global(.token.entity),
	:global(.token.url) {
		color: #f9a8d4;
	}

	:global(.token.class-name),
	:global(.token.type) {
		color: #fde68a;
	}

	:global(.token.directive),
	:global(.token.important),
	:global(.token.regex) {
		color: #fca5a5;
	}

	:global(.token.property) {
		color: #a5b4fc;
	}

	:global(h1) {
		font-size: 2.25rem;
		letter-spacing: -0.025em;
	}

	:global(h2) {
		font-size: 1.5rem;
		margin-top: 2.5rem;
		margin-bottom: 1rem;
		padding-bottom: 0.5rem;
		border-bottom: 2px solid #e0f2fe;
	}

	:global(h3) {
		font-size: 1.25rem;
		margin-top: 2rem;
		margin-bottom: 0.75rem;
	}

	:global(h4) {
		font-size: 1.1rem;
		margin-top: 1.75rem;
		margin-bottom: 0.5rem;
	}

	:global(p) {
		margin-bottom: 1rem;
	}

	:global(ul, ol) {
		margin-bottom: 1rem;
		padding-left: 1.5rem;
	}

	:global(li) {
		margin-bottom: 0.4rem;
	}

	:global(hr) {
		border: none;
		border-top: 2px solid #e5e7eb;
		margin: 2.5rem 0;
	}

	:global(.table-wrap) {
		overflow-x: auto;
		margin: 1.5rem 0;
	}

:global(table) {
		width: 100%;
		border-collapse: collapse;
		font-size: 0.9rem;
	}

	:global(thead) {
		text-align: left;
	}

	:global(th) {
		font-family: 'Inter', system-ui, sans-serif;
		font-weight: 600;
		padding: 0.75rem 1rem;
		border-bottom: 2px solid #0891b2;
		color: #1a1a2e;
		white-space: nowrap;
	}

	:global(td) {
		padding: 0.5rem 1rem;
		border-bottom: 1px solid #e5e7eb;
		font-family: 'JetBrains Mono', monospace;
		font-size: 0.85rem;
	}

	:global(tbody tr:hover) {
		background: #ecfeff;
	}

	.site {
		display: flex;
		flex-direction: column;
		min-height: 100vh;
	}

	header {
		background: #1a1a2e;
		border-bottom: 3px solid #22d3ee;
		position: sticky;
		top: 0;
		z-index: 100;
	}

	.header-inner {
		max-width: 900px;
		margin: 0 auto;
		padding: 0 1.5rem;
		display: flex;
		align-items: center;
		justify-content: space-between;
		height: 60px;
	}

	.logo {
		font-family: 'JetBrains Mono', monospace;
		font-size: 1.25rem;
		font-weight: 700;
		color: #fff;
		letter-spacing: -0.02em;
	}

	.logo:hover {
		color: #a9fefe;
		text-decoration: none;
	}

	nav {
		display: flex;
		gap: 1.5rem;
	}

	nav a {
		font-family: 'Inter', system-ui, sans-serif;
		font-size: 0.875rem;
		font-weight: 500;
		color: #a5a5c0;
		transition: color 0.15s;
	}

	nav a:hover {
		color: #fff;
		text-decoration: none;
	}

	nav a.active {
		color: #a9fefe;
	}

	main {
		flex: 1;
		max-width: 900px;
		width: 100%;
		margin: 0 auto;
		padding: 2.5rem 1.5rem;
	}

	footer {
		background: #1a1a2e;
		color: #a5a5c0;
		margin-top: auto;
	}

	.footer-inner {
		max-width: 900px;
		margin: 0 auto;
		padding: 2rem 1.5rem;
	}

	.disclaimer {
		background: rgba(34, 211, 238, 0.08);
		border: 1px solid rgba(34, 211, 238, 0.2);
		border-radius: 8px;
		padding: 1rem 1.25rem;
		margin-bottom: 1.5rem;
		font-size: 0.875rem;
		line-height: 1.6;
	}

	.disclaimer p {
		margin: 0;
	}

	.disclaimer a {
		color: #67e8f9;
	}

	.disclaimer a:hover {
		color: #a9fefe;
	}

	.copyright {
		font-size: 0.8rem;
		text-align: center;
	}

	.copyright p {
		margin: 0;
	}

	.copyright a {
		color: #67e8f9;
	}

	.toc {
		position: fixed;
		top: 100px;
		left: calc(50% + 480px);
		width: 220px;
		max-height: calc(100vh - 140px);
		overflow-y: auto;
		z-index: 50;
	}

	.toc nav {
		display: flex;
		flex-direction: column;
	}

	.toc-title {
		font-family: 'Inter', system-ui, sans-serif;
		font-size: 0.7rem;
		font-weight: 600;
		text-transform: uppercase;
		letter-spacing: 0.05em;
		color: #9ca3af;
		margin-bottom: 0.4rem;
	}

	.toc-link {
		font-family: 'Inter', system-ui, sans-serif;
		font-size: 0.75rem;
		line-height: 1.3;
		color: #9ca3af;
		padding: 0.1rem 0 0.1rem 0.75rem;
		border-left: 2px solid transparent;
		transition: color 0.15s, border-color 0.15s;
	}

	.toc-link:hover {
		color: #1a1a2e;
		text-decoration: none;
	}

	.toc-link.active {
		color: #0e7490;
		border-left-color: #0e7490;
	}

	.toc-link.toc-h3 {
		padding-left: 1.25rem;
	}

	.toc-link.toc-h4 {
		padding-left: 1.75rem;
	}

	@media (max-width: 1280px) {
		.toc {
			display: none;
		}
	}

	@media (max-width: 640px) {
		.header-inner {
			flex-direction: column;
			height: auto;
			padding: 0.75rem 1.5rem;
			gap: 0.5rem;
		}

		nav {
			gap: 1rem;
		}

		:global(h1) {
			font-size: 1.75rem;
		}

		:global(table) {
			font-size: 0.8rem;
		}

		:global(td), :global(th) {
			padding: 0.4rem 0.5rem;
		}
	}
</style>
