<svelte:head>
	<title>BPF.WTF — The Archived Works of Richard Patel</title>
	<meta name="description" content="Archive of Richard Patel's Solana BPF assembly articles covering the SBF instruction set and Sealevel syscalls. Maintained by Blueshift with permission." />
	<meta property="og:title" content="BPF.WTF — The Archived Works of Richard Patel" />
	<meta property="og:description" content="Archive of Richard Patel's Solana BPF assembly articles covering the SBF instruction set and Sealevel syscalls." />
	<meta property="og:type" content="website" />
	<meta property="og:url" content="https://bpf.wtf" />
	<meta name="twitter:card" content="summary" />
	<meta name="twitter:title" content="BPF.WTF — The Archived Works of Richard Patel" />
	<meta name="twitter:description" content="Archive of Richard Patel's Solana BPF assembly articles covering the SBF instruction set and Sealevel syscalls." />
	<link rel="canonical" href="https://bpf.wtf" />
</svelte:head>

<div class="hero">
	<h1>BPF.WTF</h1>
	<p class="description">
		An archive of Richard Patel's essential reference material on Solana BPF assembly. 
		These articles remain some of the most valuable low-level documentation for Solana developers working with the virtual machine.
	</p>
</div>

<div class="articles">
	<a href="/sol-0x03-isa" class="article-card">
		<h2>0x03: SBF Instruction Set</h2>
		<span class="article-tag">Solana BPF Series</span>
		<p>
			A comprehensive reference of all Solana Bytecode Format (SBF) opcodes, including
			load/store operations, 32-bit and 64-bit ALU instructions, endian conversions,
			jump/branch instructions, and the call class. Covers rbpf, Capstone, and LLVM
			disassembly flavors.
		</p>
		<span class="article-meta">
			<span>By Richard Patel (<span class="author-handle">@fd_ripatel</span>)</span>
			<span>Jul 8, 2022</span>
		</span>
	</a>

	<a href="/sol-0x04-syscalls" class="article-card">
		<h2>0x04: Sealevel Syscalls</h2>
		<span class="article-tag">Solana BPF Series</span>
		<p>
			Complete documentation of all syscalls available in Solana's Sealevel VM, including
			logging, cryptographic hashes (SHA-256, Keccak-256, BLAKE3), elliptic curve operations,
			PDA derivation, cross-program invocation (CPI), memory operations, and sysvar access.
			Includes C function signatures and calling conventions.
		</p>
		<span class="article-meta">
			<span>By Richard Patel (<span class="author-handle">@fd_ripatel</span>)</span>
			<span>Jul 9, 2022</span>
		</span>
	</a>
</div>

<div class="notice">
	<h3>About this archive</h3>
	<p>
		The original <strong>bpf.wtf</strong> domain lapsed and was at risk of being repurposed.
		<a href="https://blueshift.gg" target="_blank" rel="noopener">Blueshift</a> acquired the
		domain to preserve these valuable resources for the Solana community. The content is
		republished here with Richard Patel's permission.
	</p>
	<p>
		Note that the original content may be outdated or conflict with current on-chain behavior,
		as it was originally written in mid-2022. As such, Blueshift and other open source contributors 
		will periodically update and maintain this content to ensure it remains accurate. You can see
		what has been updated from our <a href="https://3f15aaf1.bpf-wtf.pages.dev/" target="_blank">Github repository</a>.
	</p>
</div>

<style>
	.hero {
		text-align: center;
		padding: 2rem 0 3rem;
		border-bottom: 2px solid #e5e7eb;
		margin-bottom: 3rem;
	}

	.hero h1 {
		font-family: 'JetBrains Mono', monospace;
		font-size: 3rem;
		letter-spacing: -0.04em;
		background: linear-gradient(135deg, #0891b2, #a9fefe);
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
		background-clip: text;
		margin-bottom: 0.5rem;
	}

	.subtitle {
		font-family: 'Inter', system-ui, sans-serif;
		font-size: 1.125rem;
		font-weight: 500;
		color: #6b7280;
		margin-bottom: 1.25rem;
	}

	.description {
		max-width: 600px;
		margin: 0 auto;
		color: #4b5563;
		font-size: 1rem;
	}

	.articles {
		display: grid;
		gap: 1.5rem;
		margin-bottom: 3rem;
	}

	.article-card {
		display: block;
		background: #fff;
		border: 1px solid #e5e7eb;
		border-radius: 12px;
		padding: 1.75rem;
		transition: border-color 0.2s, box-shadow 0.2s;
		color: inherit;
	}

	.article-card:hover {
		border-color: #22d3ee;
		box-shadow: 0 4px 24px rgba(34, 211, 238, 0.15);
		text-decoration: none;
	}

	.article-tag {
		font-family: 'Inter', system-ui, sans-serif;
		font-size: 0.75rem;
		font-weight: 600;
		text-transform: uppercase;
		letter-spacing: 0.05em;
		color: #0891b2;
		background: #ecfeff;
		padding: 0.25rem 0.75rem;
		border-radius: 100px;
	}

	.article-card h2 {
		font-size: 1.375rem;
		margin: 0 0 0.5rem;
		border: none;
		padding: 0;
	}

	.article-card .article-tag {
		display: inline-block;
		margin-bottom: 0.75rem;
	}

	.author-handle {
		font-family: 'JetBrains Mono', monospace;
		font-size: 0.75rem;
		color: #0891b2;
	}

	.article-card p {
		color: #4b5563;
		font-size: 0.95rem;
		line-height: 1.65;
		margin-bottom: 1rem;
	}

	.article-meta {
		display: flex;
		gap: 1.5rem;
		font-family: 'Inter', system-ui, sans-serif;
		font-size: 0.8rem;
		color: #9ca3af;
	}

	.notice {
		background: #fffbeb;
		border: 1px solid #fde68a;
		border-radius: 12px;
		padding: 1.5rem 1.75rem;
	}

	.notice h3 {
		font-size: 1rem;
		margin-bottom: 0.75rem;
		color: #92400e;
	}

	.notice p {
		font-size: 0.9rem;
		color: #78350f;
		line-height: 1.65;
	}

	.notice p:last-child {
		margin-bottom: 0;
	}

	@media (max-width: 640px) {
		.hero h1 {
			font-size: 2.25rem;
		}

		.article-card {
			padding: 1.25rem;
		}
	}
</style>
