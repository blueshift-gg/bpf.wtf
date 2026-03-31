<svelte:head>
	<title>0x03: SBF Instruction Set — BPF.WTF</title>
	<meta name="description" content="Complete reference of all Solana Bytecode Format (SBF) opcodes including load/store, ALU, jump, and call instructions." />
	<meta property="og:title" content="0x03: SBF Instruction Set — BPF.WTF" />
	<meta property="og:description" content="All Solana Bytecode Format (SBF) opcodes" />
	<meta property="og:type" content="article" />
	<meta property="og:url" content="https://bpf.wtf/sol-0x03-isa" />
	<meta property="article:published_time" content="2022-07-08T16:26:17.000Z" />
	<meta property="article:author" content="Richard Patel" />
	<meta property="article:tag" content="Solana BPF Series" />
	<meta name="twitter:card" content="summary" />
	<meta name="twitter:title" content="0x03: SBF Instruction Set — BPF.WTF" />
	<meta name="twitter:description" content="All Solana Bytecode Format (SBF) opcodes" />
	<link rel="canonical" href="https://bpf.wtf/sol-0x03-isa" />
</svelte:head>

<article>
	<header class="article-header">
		<span class="article-meta">
			By <a href="https://x.com/fd_ripatel" target="_blank" rel="noopener" class="author-link"><strong>Richard Patel</strong></a> in
			<span class="tag">Solana BPF Series</span>
			— <time datetime="2022-07-08">Jul 8, 2022</time>
		</span>
		<h1>~bpf.sol | 0x03: SBF Instruction Set</h1>
		<p class="excerpt">All Solana Bytecode Format (SBF) opcodes</p>
	</header>

	<div class="content">
		<h3 id="sources"><a href="#sources" class="anchor-wrap"><span class="anchor-hash">#</span>Sources</a></h3>
		<p>The family of eBPF ISAs lacks a specification document as you may know it from other architectures. Instead, each flavor of eBPF is defined by its VM implementation. As such, Solana's SBF is currently defined by the <a href="https://docs.rs/solana_rbpf" target="_blank" rel="noopener">solana_rbpf</a> Rust crate.</p>
		<p>All info below was compiled together from these sources:</p>
		<ul>
			<li><code>solana_rbpf::ebpf</code>, which defines SBF opcodes.<br/>
				<a href="https://github.com/solana-labs/rbpf/blob/main/src/ebpf.rs" target="_blank" rel="noopener">https://github.com/solana-labs/rbpf/blob/main/src/ebpf.rs</a></li>
			<li><code>solana_rbpf::disassembler</code>, which implements rbpf-style disassembly.<br/>
				<a href="https://github.com/solana-labs/rbpf/blob/main/src/disassembler.rs" target="_blank" rel="noopener">https://github.com/solana-labs/rbpf/blob/main/src/disassembler.rs</a></li>
			<li>The <code>BPF</code> target of Solana's LLVM fork, which defines the register model, bytecode encoding, and pseudocode assembly form.<br/>
				<a href="https://github.com/solana-labs/llvm-project/tree/solana-rustc/13.0-2021-09-30/llvm/lib/Target/BPF" target="_blank" rel="noopener">https://github.com/solana-labs/llvm-project/tree/solana-rustc/13.0-2021-09-30/llvm/lib/Target/BPF</a></li>
			<li>The Capstone <code>BPF</code> backend, which includes an eBPF disassembler<br/>
				<a href="https://github.com/capstone-engine/capstone/tree/next/arch/BPF" target="_blank" rel="noopener">https://github.com/capstone-engine/capstone/tree/next/arch/BPF</a></li>
		</ul>
		<p><em>Note: This source is not authoritative. It may be outdated or conflict with actual on-chain behavior. If you find any such bugs, please consider sending corrections. And if this document helps you build software, please attribute this source – thanks!</em></p>

		<h3 id="assembler"><a href="#assembler" class="anchor-wrap"><span class="anchor-hash">#</span>Assembler</a></h3>
		<p>Several flavors of SBF disassembly exist. Unfortunately, they are not mutually compatible.</p>
		<ul>
			<li><strong>rbpf-style:</strong> prefix notation with C-style indirect addressing.<br/>
				Seen in <code>rbpf-cli</code> and tools based on the Rust implementation of Solana.</li>
			<li><strong>Capstone-style:</strong> similar to rbpf-style with different ALU mnemonics.<br/>
				Seen in binary analysis tools like <code>radare2</code>.</li>
			<li><strong>LLVM-style:</strong> infix notation resembling C pseudocode.<br/>
				Seen with LLVM tools used in the build process.</li>
		</ul>

		<h3 id="opcode-tables"><a href="#opcode-tables" class="anchor-wrap"><span class="anchor-hash">#</span>Opcode Tables</a></h3>

		<h4 id="legacy-loadstore-class"><a href="#legacy-loadstore-class" class="anchor-wrap"><span class="anchor-hash">#</span>Legacy Load/Store class</a></h4>
		<p>The <code>BPF_LD</code> opcode family consists of special purpose opcodes inherited from Linux eBPF where they were used to access network packet input data.</p>

		<div class="table-wrap">
			<table>
				<thead><tr><th>Opcode</th><th>rbpf</th><th>Capstone</th><th>LLVM</th><th>Notes</th></tr></thead>
				<tbody>
					<tr><td>0x18</td><td>lddw</td><td>lddw</td><td>r1 = 0x42 ll</td><td>Two insns long</td></tr>
					<tr><td>0x30</td><td>ldabsb</td><td></td><td>r1 = *(u8  *)skb[3]</td><td>Deprecated</td></tr>
					<tr><td>0x28</td><td>ldabsh</td><td></td><td>r1 = *(u16 *)skb[3]</td><td>Deprecated</td></tr>
					<tr><td>0x20</td><td>ldabsw</td><td></td><td>r1 = *(u32 *)skb[3]</td><td>Deprecated</td></tr>
					<tr><td>0x38</td><td>ldabsdw</td><td></td><td>r1 = *(u64 *)skb[3]</td><td>Deprecated</td></tr>
					<tr><td>0x50</td><td>ldindb</td><td></td><td>r1 = *(u8  *)skb[r0]</td><td>Deprecated</td></tr>
					<tr><td>0x48</td><td>ldindh</td><td></td><td>r1 = *(u16 *)skb[r0]</td><td>Deprecated</td></tr>
					<tr><td>0x40</td><td>ldindw</td><td></td><td>r1 = *(u32 *)skb[r0]</td><td>Deprecated</td></tr>
					<tr><td>0x58</td><td>ldinddw</td><td></td><td>r1 = *(u64 *)skb[r0]</td><td>Deprecated</td></tr>
				</tbody>
			</table>
		</div>

		<p><code>ldabs*</code> used to access instruction input data starting at <code>0x4_0000_0000</code> and <code>ldind*</code> was the indirect version thereof. These instructions are disabled now and have been replaced by the new load/store class.</p>
		<p>Only <code>lddw</code> remains enabled on mainnet today. It moves a 64-bit immediate into a GPR. This makes it more of a 64-bit variant of <code>mov32</code> than an actual load instruction. (Note that <code>mov64</code> with an immediate is a synonym to <code>mov32</code> – the name is misleading).</p>

		<h4 id="loadstore-class"><a href="#loadstore-class" class="anchor-wrap"><span class="anchor-hash">#</span>Load/Store class</a></h4>
		<p>The <code>BPF_LDX</code> and <code>BPF_STX</code> classes provide common memory operations.<br/>
			Memory is referenced using a base address from a GPR and an immediate offset.</p>
		<p>Three operation groups are available:</p>
		<ul>
			<li><code>ldx*</code>: Load value from memory into register (8-bit, 16-bit, 32-bit, 64-bit)</li>
			<li><code>st*</code>: Store immediate value into memory (8-bit, 16-bit, 32-bit, 32-bit zero extended to 64-bit)</li>
			<li><code>stx*</code>: Store value from register into memory (8-bit, 16-bit, 32-bit, 64-bit)</li>
		</ul>

		<div class="table-wrap">
			<table>
				<thead><tr><th>Opcode</th><th>rbpf</th><th>Capstone</th><th>LLVM</th><th>Notes</th></tr></thead>
				<tbody>
					<tr><td>0x71</td><td>ldxb</td><td>ldxb</td><td>r1 = *(u8  *)(r2 + 42)</td><td></td></tr>
					<tr><td>0x69</td><td>ldxh</td><td>ldxh</td><td>r1 = *(u16 *)(r2 + 42)</td><td></td></tr>
					<tr><td>0x61</td><td>ldxw</td><td>ldxw</td><td>r1 = *(u32 *)(r2 + 42)</td><td></td></tr>
					<tr><td>0x79</td><td>ldxdw</td><td>ldxdw</td><td>r1 = *(u64 *)(r2 + 42)</td><td></td></tr>
					<tr><td>0x72</td><td>stb</td><td>stb</td><td>*(u8  *)(r2 + 42) = 69</td><td></td></tr>
					<tr><td>0x6a</td><td>sth</td><td>sth</td><td>*(u16 *)(r2 + 42) = 69</td><td></td></tr>
					<tr><td>0x62</td><td>stw</td><td>stw</td><td>*(u32 *)(r2 + 42) = 69</td><td></td></tr>
					<tr><td>0x7a</td><td>stdw</td><td>stdw</td><td>*(u64 *)(r2 + 42) = 69</td><td>Immediate is 32-bit</td></tr>
					<tr><td>0x73</td><td>stxb</td><td>stxb</td><td>*(u8  *)(r2 + 42) = r1</td><td></td></tr>
					<tr><td>0x6b</td><td>stxh</td><td>stxh</td><td>*(u16 *)(r2 + 42) = r1</td><td></td></tr>
					<tr><td>0x63</td><td>stxw</td><td>stxw</td><td>*(u32 *)(r2 + 42) = r1</td><td></td></tr>
					<tr><td>0x7b</td><td>stxdw</td><td>stxdw</td><td>*(u64 *)(r2 + 42) = r1</td><td></td></tr>
				</tbody>
			</table>
		</div>

		<h4 id="64-bit-alu-class"><a href="#64-bit-alu-class" class="anchor-wrap"><span class="anchor-hash">#</span>64-bit ALU class</a></h4>
		<p>The 64-bit ALU instructions operate on general-purpose registers. The stack pointer (r10) can only be used as a source operand.</p>
		<p>Each operation has two forms:</p>
		<ul>
			<li>rD &larr; OP(rD, imm)</li>
			<li>rD &larr; OP(rD, rS)</li>
		</ul>

		<div class="table-wrap">
			<table>
				<thead><tr><th>Opcode</th><th>rbpf</th><th>Capstone</th><th>LLVM</th><th>Notes</th></tr></thead>
				<tbody>
					<tr><td>0x07</td><td>add64</td><td>add64</td><td>r1 += 0x42</td><td></td></tr>
					<tr><td>0x0f</td><td>add64</td><td>add64</td><td>r1 += r2</td><td></td></tr>
					<tr><td>0x17</td><td>sub64</td><td>sub64</td><td>r1 -= 0x42</td><td></td></tr>
					<tr><td>0x1f</td><td>sub64</td><td>sub64</td><td>r1 -= r2</td><td></td></tr>
					<tr><td>0x27</td><td>mul64</td><td>mul64</td><td>r1 *= 0x42</td><td></td></tr>
					<tr><td>0x2f</td><td>mul64</td><td>mul64</td><td>r1 *= r2</td><td></td></tr>
					<tr><td>0x37</td><td>div64</td><td>div64</td><td>r1 /= 0x42</td><td></td></tr>
					<tr><td>0x3f</td><td>div64</td><td>div64</td><td>r1 /= r2</td><td></td></tr>
					<tr><td>0x47</td><td>or64</td><td>or64</td><td>r1 |= 0x42</td><td></td></tr>
					<tr><td>0x4f</td><td>or64</td><td>or64</td><td>r1 |= r2</td><td></td></tr>
					<tr><td>0x57</td><td>and64</td><td>and64</td><td>r1 &amp;= 0x42</td><td></td></tr>
					<tr><td>0x5f</td><td>and64</td><td>and64</td><td>r1 &amp;= r2</td><td></td></tr>
					<tr><td>0x67</td><td>lsh64</td><td>lsh64</td><td>r1 &lt;&lt;= 0x42</td><td></td></tr>
					<tr><td>0x6f</td><td>lsh64</td><td>lsh64</td><td>r1 &lt;&lt;= r2</td><td></td></tr>
					<tr><td>0x77</td><td>rsh64</td><td>rsh64</td><td>r1 &gt;&gt;= 0x42</td><td></td></tr>
					<tr><td>0x7f</td><td>rsh64</td><td>rsh64</td><td>r1 &gt;&gt;= r2</td><td></td></tr>
					<tr><td>0x87</td><td>neg64</td><td>neg64</td><td>r1 = -r1</td><td></td></tr>
					<tr><td>0x97</td><td>mod64</td><td>mod64</td><td>r1 %= 0x42</td><td></td></tr>
					<tr><td>0x9f</td><td>mod64</td><td>mod64</td><td>r1 %= r2</td><td></td></tr>
					<tr><td>0xa7</td><td>xor64</td><td>xor64</td><td>r1 ^= 0x42</td><td></td></tr>
					<tr><td>0xaf</td><td>xor64</td><td>xor64</td><td>r1 ^= r2</td><td></td></tr>
					<tr><td>0xb7</td><td>mov64</td><td>mov64</td><td>r1 = 0x42</td><td>same as mov32</td></tr>
					<tr><td>0xbf</td><td>mov64</td><td>mov64</td><td>r1 = r2</td><td></td></tr>
					<tr><td>0xc7</td><td>arsh64</td><td>arsh64</td><td></td><td></td></tr>
					<tr><td>0xcf</td><td>arsh64</td><td>arsh64</td><td></td><td></td></tr>
					<tr><td>0xe7</td><td>sdiv64</td><td></td><td></td><td></td></tr>
					<tr><td>0xef</td><td>sdiv64</td><td></td><td></td><td></td></tr>
				</tbody>
			</table>
		</div>

		<h4 id="32-bit-alu-class"><a href="#32-bit-alu-class" class="anchor-wrap"><span class="anchor-hash">#</span>32-bit ALU class</a></h4>
		<p>The 32-bit ALU instructions mostly follow their 64-bit counterparts. They operate on the lower word of the input registers. The upper half of destination registers gets implicitly zeroed.</p>

		<div class="table-wrap">
			<table>
				<thead><tr><th>Opcode</th><th>rbpf</th><th>Capstone</th><th>LLVM</th></tr></thead>
				<tbody>
					<tr><td>0x04</td><td>add32</td><td>add</td><td>w1 += 0x42</td></tr>
					<tr><td>0x0c</td><td>add32</td><td>add</td><td>w1 += w2</td></tr>
					<tr><td>0x14</td><td>sub32</td><td>sub</td><td>w1 -= 0x42</td></tr>
					<tr><td>0x1c</td><td>sub32</td><td>sub</td><td>w1 -= w2</td></tr>
					<tr><td>0x24</td><td>mul32</td><td>mul</td><td>w1 *= 0x42</td></tr>
					<tr><td>0x2c</td><td>mul32</td><td>mul</td><td>w1 *= w2</td></tr>
					<tr><td>0x34</td><td>div32</td><td>div</td><td>w1 /= 0x42</td></tr>
					<tr><td>0x3c</td><td>div32</td><td>div</td><td>w1 /= w2</td></tr>
					<tr><td>0x44</td><td>or32</td><td>or</td><td>w1 |= 0x42</td></tr>
					<tr><td>0x4c</td><td>or32</td><td>or</td><td>w1 |= w2</td></tr>
					<tr><td>0x54</td><td>and32</td><td>and</td><td>w1 &amp;= 0x42</td></tr>
					<tr><td>0x5c</td><td>and32</td><td>and</td><td>w1 &amp;= w2</td></tr>
					<tr><td>0x64</td><td>lsh32</td><td>lsh</td><td>w1 &lt;&lt;= 0x42</td></tr>
					<tr><td>0x6c</td><td>lsh32</td><td>lsh</td><td>w1 &lt;&lt;= w2</td></tr>
					<tr><td>0x74</td><td>rsh32</td><td>rsh</td><td>w1 &gt;&gt;= 0x42</td></tr>
					<tr><td>0x7c</td><td>rsh32</td><td>rsh</td><td>w1 &gt;&gt;= w2</td></tr>
					<tr><td>0x84</td><td>neg32</td><td>neg</td><td>w1 = -w1</td></tr>
					<tr><td>0x94</td><td>mod32</td><td>mod</td><td>w1 %= 0x42</td></tr>
					<tr><td>0x9c</td><td>mod32</td><td>mod</td><td>w1 %= w2</td></tr>
					<tr><td>0xa4</td><td>xor32</td><td>xor</td><td>w1 ^= 0x42</td></tr>
					<tr><td>0xac</td><td>xor32</td><td>xor</td><td>w1 ^= w2</td></tr>
					<tr><td>0xb4</td><td>mov32</td><td>mov</td><td>w1 = 0x42</td></tr>
					<tr><td>0xbc</td><td>mov32</td><td>mov</td><td>w1 = w2</td></tr>
					<tr><td>0xc4</td><td>arsh32</td><td>arsh</td><td>w1 s&gt;&gt;= 0x42</td></tr>
					<tr><td>0xcc</td><td>arsh32</td><td>arsh</td><td>w1 s&gt;&gt;= w2</td></tr>
					<tr><td>0xe4</td><td>sdiv32</td><td></td><td></td></tr>
					<tr><td>0xec</td><td>sdiv32</td><td></td><td></td></tr>
				</tbody>
			</table>
		</div>

		<h4 id="endian-alu-extension"><a href="#endian-alu-extension" class="anchor-wrap"><span class="anchor-hash">#</span>Endian ALU extension</a></h4>

		<div class="table-wrap">
			<table>
				<thead><tr><th>Opcode</th><th>rbpf</th><th>Capstone</th><th>LLVM</th><th>Notes</th></tr></thead>
				<tbody>
					<tr><td>0xd4</td><td>le&#123;n&#125;</td><td>le&#123;n&#125;</td><td>r1 = le&#123;n&#125; r1</td><td>Basically a mask</td></tr>
					<tr><td>0xdc</td><td>be&#123;n&#125;</td><td>be&#123;n&#125;</td><td>r1 = be&#123;n&#125; r1</td><td>Swaps endianness</td></tr>
				</tbody>
			</table>
		</div>

		<p>The LE/BE instructions operate in 16-bit, 32-bit, or 64-bit mode, indicated by the values in the immediate field.<br/>
			For example, <code>0xdc</code> with destination register <code>1</code> and immediate <code>32</code> is <code>be32 r1</code>.<br/>
			They were used for portable endianness conversions. Since Solana is always little-endian, only the BE instruction swaps bytes.</p>
		<ul>
			<li><code>le16 rD</code> is equivalent to <code>rD &amp;= 0xFFFF</code>.</li>
			<li><code>le32 rD</code> is equivalent to <code>rD &amp;= 0xFFFF_FFFF</code>.</li>
			<li><code>le64 rD</code> is a <code>nop</code>.</li>
			<li><code>be16 rD</code> swaps the lower 2 bytes and zeroes the upper 6.</li>
			<li><code>be32 rD</code> reverses the order of the lower 4 bytes and zeros the upper 4.</li>
			<li><code>be64 rD</code> reverses the order of all 8 bytes.</li>
		</ul>

		<h4 id="jump-class"><a href="#jump-class" class="anchor-wrap"><span class="anchor-hash">#</span>Jump class</a></h4>
		<p>The jump instructions combine comparisons and conditional jumps. Compared to x86 or PowerPC, this simplifies the ISA by removing the condition register and using less opcodes.</p>
		<p>The conditional jump instructions (all except <code>ja</code>) compare a register either against another register or an immediate value.</p>

		<div class="table-wrap">
			<table>
				<thead><tr><th>Opcode</th><th>rbpf</th><th>Capstone</th><th>LLVM</th></tr></thead>
				<tbody>
					<tr><td>0x05</td><td>ja</td><td>jmp</td><td>goto +12</td></tr>
					<tr><td>0x15</td><td>jeq</td><td>jeq</td><td>if r0 == r1 goto +12</td></tr>
					<tr><td>0x1d</td><td>jeq</td><td>jeq</td><td>if r0 == 42 goto +12</td></tr>
					<tr><td>0x25</td><td>jgt</td><td>jgt</td><td>if r0 &gt; r1 goto +12</td></tr>
					<tr><td>0x2d</td><td>jgt</td><td>jgt</td><td>if r0 &gt; 42 goto +12</td></tr>
					<tr><td>0x35</td><td>jge</td><td>jge</td><td>if r0 &gt;= r1 goto +12</td></tr>
					<tr><td>0x3d</td><td>jge</td><td>jge</td><td>if r0 &gt;= 42 goto +12</td></tr>
					<tr><td>0x45</td><td>jset</td><td>jset</td><td>if r0 &amp; r1 != 0 goto +12</td></tr>
					<tr><td>0x4d</td><td>jset</td><td>jset</td><td>if r0 &amp; 42 != 0 goto +12</td></tr>
					<tr><td>0x55</td><td>jne</td><td>jne</td><td>if r0 != r1 goto +12</td></tr>
					<tr><td>0x5d</td><td>jne</td><td>jne</td><td>if r0 != 42 goto +12</td></tr>
					<tr><td>0x65</td><td>jsgt</td><td>jsgt</td><td>if r0 s&gt; r1 goto +12</td></tr>
					<tr><td>0x6d</td><td>jsgt</td><td>jsgt</td><td>if r0 s&gt; 42 goto +12</td></tr>
					<tr><td>0x75</td><td>jsge</td><td>jsge</td><td>if r0 s&gt;= r1 goto +12</td></tr>
					<tr><td>0x7d</td><td>jsge</td><td>jsge</td><td>if r0 s&gt;= 42 goto +12</td></tr>
					<tr><td>0xa5</td><td>jlt</td><td>jlt</td><td>if r0 &lt; r1 goto +12</td></tr>
					<tr><td>0xad</td><td>jlt</td><td>jlt</td><td>if r0 &lt; 42 goto +12</td></tr>
					<tr><td>0xb5</td><td>jle</td><td>jle</td><td>if r0 &lt;= r1 goto +12</td></tr>
					<tr><td>0xbd</td><td>jle</td><td>jle</td><td>if r0 &lt;= 42 goto +12</td></tr>
					<tr><td>0xc5</td><td>jslt</td><td>jslt</td><td>if r0 s&lt; r1 goto +12</td></tr>
					<tr><td>0xcd</td><td>jslt</td><td>jslt</td><td>if r0 s&lt; 42 goto +12</td></tr>
					<tr><td>0xd5</td><td>jsle</td><td>jsle</td><td>if r0 s&lt;= r1 goto +12</td></tr>
					<tr><td>0xdd</td><td>jsle</td><td>jsle</td><td>if r0 s&lt;= 42 goto +12</td></tr>
				</tbody>
			</table>
		</div>

		<h4 id="call-class"><a href="#call-class" class="anchor-wrap"><span class="anchor-hash">#</span>Call class</a></h4>
		<p>The call-related opcodes push/pop the call frame stack and stack pointer.<br/>
			The call frame stack is a protected data structure that can only be accessed by the call class (comparable to x86 shadow stacks).</p>

		<div class="table-wrap">
			<table>
				<thead><tr><th>Opcode</th><th>rbpf</th><th>Capstone</th><th>LLVM</th><th>Notes</th></tr></thead>
				<tbody>
					<tr><td>0x85</td><td>call</td><td>call</td><td>call 0x1234</td><td></td></tr>
					<tr><td>0x8D</td><td>callx</td><td>callx</td><td>callx r3</td><td>Not part of kernel eBPF. Register idx in imm field</td></tr>
					<tr><td>0x95</td><td>exit</td><td>exit</td><td>exit</td><td></td></tr>
				</tbody>
			</table>
		</div>

		<ul>
			<li><code>call</code> saves a call frame and enters a syscall or jumps to a target indicated by the immediate.</li>
			<li><code>callx</code> saves a call frame and jumps to the absolute address in the given register. The register index is stored in the immediate field of the instruction.</li>
			<li><code>exit</code> restores a call frame and jumps to its return address.</li>
		</ul>

		<p><strong>Resolving call targets</strong></p>
		<p>The 32-bit immediate field of the <code>call</code> opcode contains the hash of target symbol name or syscall name. The VM resolves a call target hash using two immutable lookup maps for syscalls and jump targets, which are constructed on program load. Syscalls take precedence over jump targets. The VM aborts when a hash cannot be resolved.</p>
		<p>The hash algorithm is Murmur3 in 32-bit mode on the UTF-8 encoding of the symbol name.</p>
		<p>See the next post for the syscalls available in Sealevel: <a href="/sol-0x04-syscalls">0x04: Sealevel Syscalls</a>.</p>
	</div>

	<nav class="article-nav">
		<div></div>
		<a href="/sol-0x04-syscalls" class="nav-next">
			Next: 0x04: Sealevel Syscalls &rarr;
		</a>
	</nav>
</article>

<style>
	article {
		max-width: 100%;
	}

	.article-header {
		margin-bottom: 2.5rem;
		padding-bottom: 1.5rem;
		border-bottom: 2px solid #e5e7eb;
	}

	.article-meta {
		font-family: 'Inter', system-ui, sans-serif;
		font-size: 0.875rem;
		color: #6b7280;
		display: block;
		margin-bottom: 0.75rem;
	}

	.author-link {
		color: #4b5563;
	}

	.author-link:hover {
		color: #0e7490;
	}

	.tag {
		color: #0891b2;
		font-weight: 500;
	}

	.excerpt {
		font-size: 1.125rem;
		color: #6b7280;
		margin-top: 0.5rem;
	}

	.content {
		line-height: 1.75;
	}

	.table-wrap {
		overflow-x: auto;
		margin: 1.5rem 0;
		border: 1px solid #e5e7eb;
		border-radius: 8px;
	}

	.table-wrap :global(table) {
		margin: 0;
		min-width: 650px;
	}

	.article-nav {
		display: flex;
		justify-content: space-between;
		margin-top: 3rem;
		padding-top: 1.5rem;
		border-top: 2px solid #e5e7eb;
	}

	.nav-next {
		font-family: 'Inter', system-ui, sans-serif;
		font-weight: 600;
		font-size: 0.95rem;
		margin-left: auto;
	}
</style>
