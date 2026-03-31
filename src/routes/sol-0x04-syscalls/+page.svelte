<script>
	import { onMount } from 'svelte';
	import Prism from 'prismjs';
	import 'prismjs/components/prism-c.js';

	onMount(() => {
		Prism.highlightAll();
	});
</script>

<svelte:head>
	<title>0x04: Sealevel Syscalls — BPF.WTF</title>
	<meta name="description" content="Complete reference of all syscalls available in Solana's Sealevel VM including logging, cryptographic hashes, PDA derivation, CPI, and memory operations." />
	<meta property="og:title" content="0x04: Sealevel Syscalls — BPF.WTF" />
	<meta property="og:description" content="This page lists the syscalls available in the Sealevel VM." />
	<meta property="og:type" content="article" />
	<meta property="og:url" content="https://bpf.wtf/sol-0x04-syscalls" />
	<meta property="article:published_time" content="2022-07-09T01:12:32.000Z" />
	<meta property="article:author" content="Richard Patel" />
	<meta property="article:tag" content="Solana BPF Series" />
	<meta name="twitter:card" content="summary" />
	<meta name="twitter:title" content="0x04: Sealevel Syscalls — BPF.WTF" />
	<meta name="twitter:description" content="This page lists the syscalls available in the Sealevel VM." />
	<link rel="canonical" href="https://bpf.wtf/sol-0x04-syscalls" />
</svelte:head>

<article>
	<header class="article-header">
		<span class="article-meta">
			By <a href="https://x.com/fd_ripatel" target="_blank" rel="noopener" class="author-link"><strong>Richard Patel</strong></a> in
			<span class="tag">Solana BPF Series</span>
			— <time datetime="2022-07-09">Jul 9, 2022</time>
		</span>
		<h1>~bpf.sol | 0x04: Sealevel Syscalls</h1>
		<p class="excerpt">This page lists the syscalls available in the Sealevel VM.</p>
	</header>

	<div class="content">
		<p>Syscalls allow programs running in the Solana virtual machine to interact with outside resources. They can be invoked using the <a href="/sol-0x03-isa#call-class"><code>call</code> instruction</a> with the immediate value set to the symbol hash.</p>

		<h2 id="syscall-table"><a href="#syscall-table" class="anchor-wrap"><span class="anchor-hash">#</span>Syscall Table</a></h2>

		<div class="table-wrap">
			<table>
				<thead><tr><th>Hash</th><th>Symbol</th></tr></thead>
				<tbody>
					<tr><td>b6fc1a11</td><td><a href="#abort">abort</a></td></tr>
					<tr><td>686093bb</td><td><a href="#sol_panic_">sol_panic_</a></td></tr>
					<tr><td>207559bd</td><td><a href="#sol_log_">sol_log_</a></td></tr>
					<tr><td>5c2a3178</td><td><a href="#sol_log_64_">sol_log_64_</a></td></tr>
					<tr><td>52ba5096</td><td><a href="#sol_log_compute_units_">sol_log_compute_units_</a></td></tr>
					<tr><td>7ef088ca</td><td><a href="#sol_log_pubkey">sol_log_pubkey</a></td></tr>
					<tr><td>9377323c</td><td><a href="#sol_create_program_address">sol_create_program_address</a></td></tr>
					<tr><td>48504a38</td><td><a href="#sol_try_find_program_address">sol_try_find_program_address</a></td></tr>
					<tr><td>11f49d86</td><td><a href="#sol_sha256">sol_sha256</a></td></tr>
					<tr><td>d7793abb</td><td><a href="#sol_keccak256">sol_keccak256</a></td></tr>
					<tr><td>17e40350</td><td><a href="#sol_secp256k1_recover">sol_secp256k1_recover</a></td></tr>
					<tr><td>174c5122</td><td><a href="#sol_blake3">sol_blake3</a></td></tr>
					<tr><td>aa2607ca</td><td><a href="#sol_curve_validate_point">sol_curve_validate_point</a></td></tr>
					<tr><td>dd1c41a6</td><td><a href="#sol_curve_group_op">sol_curve_group_op</a></td></tr>
					<tr><td>d56b5fe9</td><td><a href="#sol_get_clock_sysvar">sol_get_clock_sysvar</a></td></tr>
					<tr><td>23a29a61</td><td><a href="#sol_get_epoch_schedule_sysvar">sol_get_epoch_schedule_sysvar</a></td></tr>
					<tr><td>3b97b73c</td><td><a href="#sol_get_fees_sysvar">sol_get_fees_sysvar</a></td></tr>
					<tr><td>bf7188f6</td><td><a href="#sol_get_rent_sysvar">sol_get_rent_sysvar</a></td></tr>
					<tr><td>717cc4a3</td><td><a href="#sol_memcpy_">sol_memcpy_</a></td></tr>
					<tr><td>434371f8</td><td><a href="#sol_memmove_">sol_memmove_</a></td></tr>
					<tr><td>5fdcde31</td><td><a href="#sol_memcmp_">sol_memcmp_</a></td></tr>
					<tr><td>3770fb22</td><td><a href="#sol_memset_">sol_memset_</a></td></tr>
					<tr><td>a22b9c85</td><td><a href="#sol_invoke_signed_c">sol_invoke_signed_c</a></td></tr>
					<tr><td>d7449092</td><td><a href="#sol_invoke_signed_rust">sol_invoke_signed_rust</a></td></tr>
					<tr><td>83f00e8f</td><td><a href="#sol_alloc_free_">sol_alloc_free_</a></td></tr>
					<tr><td>a226d3eb</td><td><a href="#sol_set_return_data">sol_set_return_data</a></td></tr>
					<tr><td>5d2245e4</td><td><a href="#sol_get_return_data">sol_get_return_data</a></td></tr>
					<tr><td>7317b434</td><td><a href="#sol_log_data">sol_log_data</a></td></tr>
					<tr><td>adb8efc8</td><td><a href="#sol_get_processed_sibling_instruction">sol_get_processed_sibling_instruction</a></td></tr>
					<tr><td>85532d94</td><td><a href="#sol_get_stack_height">sol_get_stack_height</a></td></tr>
				</tbody>
			</table>
		</div>

		<h2 id="calling-convention"><a href="#calling-convention" class="anchor-wrap"><span class="anchor-hash">#</span>Calling Convention</a></h2>
		<p>The convention for invoking syscalls follows the SBFv2 ABI for function calls.</p>
		<p>Input params are provided in registers <code>r1</code>, <code>r2</code>, <code>r3</code>, <code>r4</code>, <code>r5</code>.<br/>
			Register <code>r0</code> is always set, either to the return value, or zero for void syscalls.<br/>
			Other registers are preserved. Syscalls do not allocate a call frame.</p>
		<p>If the caller provides invalid inputs that cause a runtime error (e.g. calling <code>sol_memcpy_</code> on null pointer), the VM raises the error at the call site.</p>

		<h2 id="syscall-reference"><a href="#syscall-reference" class="anchor-wrap"><span class="anchor-hash">#</span>Syscall Reference</a></h2>

		<h3 id="abort"><a href="#abort" class="anchor-wrap"><span class="anchor-hash">#</span>abort</a></h3>
		<p>See <a href="https://man7.org/linux/man-pages/man3/abort.3.html" target="_blank" rel="noopener"><code>abort(3)</code></a>. Aborts the VM with <code>SyscallError::Abort</code> and never returns.</p>
		<pre><code class="language-c">{`#include <stdlib.h>
noreturn void abort();`}</code></pre>

		<h3 id="sol_panic_"><a href="#sol_panic_" class="anchor-wrap"><span class="anchor-hash">#</span>sol_panic_</a></h3>
		<p>Aborts the VM with <code>SyscallError::Panic</code> and never returns.</p>
		<pre><code class="language-c">{`#include <sol/assert.h>
noreturn void sol_panic_(
    /* r1 */ const char *file_str,
    /* r2 */ uint64_t file_str_len,
    /* r3 */ uint64_t line,
    /* r4 */ uint64_t column
);`}</code></pre>

		<h3 id="sol_log_"><a href="#sol_log_" class="anchor-wrap"><span class="anchor-hash">#</span>sol_log_</a></h3>
		<p>Writes a log entry to the Sealevel logging facility.</p>
		<pre><code class="language-c">{`#include <sol/log.h>
void sol_log_(
    /* r1 */ const char *message,
    /* r2 */ uint64_t len
);`}</code></pre>

		<h3 id="sol_log_64_"><a href="#sol_log_64_" class="anchor-wrap"><span class="anchor-hash">#</span>sol_log_64_</a></h3>
		<p>Writes a log entry containing five 64-bit integers to the Sealevel logging facility. The log message uses format <code>Program log: %x %x %x %x %x</code>.</p>
		<pre><code class="language-c">{`#include <sol/log.h>
void sol_log_64_(
    uint64_t r1,
    uint64_t r2,
    uint64_t r3,
    uint64_t r4,
    uint64_t r5
);`}</code></pre>

		<h3 id="sol_log_compute_units_"><a href="#sol_log_compute_units_" class="anchor-wrap"><span class="anchor-hash">#</span>sol_log_compute_units_</a></h3>
		<p>Writes the current compute unit consumption to the Sealevel logging facility.<br/>
			The log message uses format <code>Program consumption: %d units remaining</code></p>
		<pre><code class="language-c">{`#include <sol/log.h>
void sol_log_compute_units_();`}</code></pre>

		<h3 id="sol_log_pubkey"><a href="#sol_log_pubkey" class="anchor-wrap"><span class="anchor-hash">#</span>sol_log_pubkey</a></h3>
		<p>Writes a log entry with a Base58-encoded Solana public key.<br/>
			The log message uses format <code>Program log: %s</code></p>
		<pre><code class="language-c">{`#include <sol/pubkey.h>
void sol_log_pubkey(/* r1 */ const SubPubkey *);`}</code></pre>

		<h3 id="sol_create_program_address"><a href="#sol_create_program_address" class="anchor-wrap"><span class="anchor-hash">#</span>sol_create_program_address</a></h3>
		<p>Calculates a <a href="https://docs.solana.com/developing/programming-model/calling-between-programs#program-derived-addresses" target="_blank" rel="noopener">program-derived address</a> (PDA) from the given program ID and seed list.</p>
		<p>If the resulting address is a valid PDA (i.e. does not lie on Curve25519), returns <code>0</code> and writes the address to the pointer in r4. Otherwise returns <code>1</code> without writing to memory.</p>
		<p>The caller is expected to provide a seeds input such that the PDA derivation succeeds. This makes <code>sol_create_program_address</code> mostly useful for verifying PDA derivations. If you want to find new PDAs, consider using <a href="#sol_try_find_program_address"><code>sol_try_find_program_address</code></a> instead.</p>
		<p>See <a href="https://docs.rs/solana-program/latest/solana_program/pubkey/struct.Pubkey.html#method.create_program_address" target="_blank" rel="noopener"><code>solana_program::pubkey::Pubkey::create_program_address</code></a>.</p>
		<pre><code class="language-c">{`#include <sol/pubkey.h>
typedef struct {
  const uint8_t *addr;
  uint64_t len;
} SolSignerSeed;

uint64_t sol_create_program_address(
    /* r1 */ const SolSignerSeed *seeds,
    /* r2 */ int seeds_len,
    /* r3 */ const SolPubkey *program_id,
    /* r4 */ SolPubkey *program_address
);`}</code></pre>

		<h3 id="sol_try_find_program_address"><a href="#sol_try_find_program_address" class="anchor-wrap"><span class="anchor-hash">#</span>sol_try_find_program_address</a></h3>
		<p>Calculates a <a href="https://docs.solana.com/developing/programming-model/calling-between-programs#program-derived-addresses" target="_blank" rel="noopener">program-derived address</a> (PDA) and bump seed from the given program ID and seed list. Since not all inputs to <code>sol_create_program_address</code> result in successful derivations, the bump seed is used a one-byte-sized nonce that is appended to the seed list to search the input space for the next valid derivation. The search starts with bump seed <code>0</code> and ends at <code>255</code>, choosing the first value that results in a valid PDA. Each iteration writes the derived address to the pointer in r4 and the bump seed byte to the pointer in r5.</p>
		<p>Returns <code>0</code> on success. In the unlikely case that the search fails, <code>1</code> is returned. In the highly unlikely failure case, bump seed <code>255</code> along with an (invalid) PDA gets written out to memory.</p>
		<p>See <a href="https://docs.rs/solana-program/latest/solana_program/pubkey/struct.Pubkey.html#method.try_find_program_address" target="_blank" rel="noopener"><code>solana_program::pubkey::Pubkey::try_find_program_address</code></a>.</p>
		<pre><code class="language-c">{`#include <sol/pubkey.h>
typedef struct {
  const uint8_t *addr;
  uint64_t len;
} SolSignerSeed;

uint64_t sol_try_find_programs_address(
    /* r1 */ const SolSignerSeed *seeds,
    /* r2 */ int seeds_len,
    /* r3 */ const SolPubkey *program_id,
    /* r4 */ SolPubkey *program_address,
    /* r5 */ uint8_t *bump_seed
);`}</code></pre>

		<h3 id="sol_sha256"><a href="#sol_sha256" class="anchor-wrap"><span class="anchor-hash">#</span>sol_sha256</a></h3>
		<p>Calculates the <a href="https://en.wikipedia.org/wiki/SHA-2" target="_blank" rel="noopener">SHA-256</a> hash for the given byte inputs. The byte slices pointed to by r1 each get hashed as if they were a contiguous array. r2 specifies the number of byte slices. It is permitted to hash overlapping byte slices.</p>
		<p>The default allowed max allowed slices count per operation is 20000. The compute unit costs are made up from two components:</p>
		<ul>
			<li><code>sha256_base_cost</code> (default <code>10</code>) once per syscall invocation.</li>
			<li>Per slice, <code>sha256_byte_cost</code> times the number of two-byte pairs per slice or minimally <code>mem_op_base_cost</code> for very short slices.</li>
		</ul>
		<pre><code class="language-c">{`#include <sol/sha.h>
typedef struct {
  const uint8_t *addr;
  uint64_t len;
} SolBytes;

void sol_sha256(
    /* r1 */ const SolBytes *bytes,
    /* r2 */ int bytes_len,
    /* r3 */ uint8_t *result
);`}</code></pre>

		<h3 id="sol_keccak256"><a href="#sol_keccak256" class="anchor-wrap"><span class="anchor-hash">#</span>sol_keccak256</a></h3>
		<p>Calculates the Keccak-256 hash for the given byte inputs. Keccak-256 is similar to NIST SHA3-256 but preserves the original padding from the <a href="https://keccak.team/files/Keccak-submission-3.pdf" target="_blank" rel="noopener">NIST competition submission</a>, not the <a href="https://csrc.nist.gov/publications/detail/fips/202/final" target="_blank" rel="noopener">FIPS 202</a> variant. This version is compatible with the <a href="https://ethereum.stackexchange.com/a/554" target="_blank" rel="noopener">Ethereum protocols</a>.</p>
		<p>The function signature and compute unit costs are identical to <a href="#sol_sha256"><code>sol_sha256</code></a>.</p>
		<pre><code class="language-c">{`#include <sol/keccak.h>
typedef struct {
  const uint8_t *addr;
  uint64_t len;
} SolBytes;

void sol_keccak256(
    /* r1 */ const SolBytes *bytes,
    /* r2 */ int bytes_len,
    /* r3 */ uint8_t *result
);`}</code></pre>

		<h3 id="sol_secp256k1_recover"><a href="#sol_secp256k1_recover" class="anchor-wrap"><span class="anchor-hash">#</span>sol_secp256k1_recover</a></h3>
		<p>Recovers a <a href="https://en.bitcoin.it/wiki/Secp256k1" target="_blank" rel="noopener">secp256k1</a> public key from a signed message. Writes the uncompressed public key (64 bytes) to the pointer in r4. Compatible with Bitcoin and Ethereum.</p>
		<p>Return codes:</p>
		<ul>
			<li><code>0x00</code>: <em>Success</em></li>
			<li><code>0x01</code>: <em>Invalid hash provided</em></li>
			<li><code>0x02</code>: <em>Invalid signature provided</em></li>
		</ul>
		<pre><code class="language-c">{`#include <sol/secp256k1.h>
uint64_t sol_secp256k1_recover(
    /* r1 */ const uint8_t *hash,
    /* r2 */ uint64_t recovery_id,
    /* r3 */ const uint8_t *signature,
    /* r4 */ uint8_t *result
);`}</code></pre>

		<h3 id="sol_blake3"><a href="#sol_blake3" class="anchor-wrap"><span class="anchor-hash">#</span>sol_blake3</a></h3>
		<p>Calculates the <a href="https://github.com/BLAKE3-team/BLAKE3" target="_blank" rel="noopener">BLAKE3</a> hash for the given byte inputs.</p>
		<p>The function signature and compute unit costs are identical to <a href="#sol_sha256"><code>sol_sha256</code></a>.</p>
		<pre><code class="language-c">{`#include <sol/blake3.h>
typedef struct {
  const uint8_t *addr;
  uint64_t len;
} SolBytes;

void sol_blake3(
    /* r1 */ const SolBytes *bytes,
    /* r2 */ int bytes_len,
    /* r3 */ uint8_t *result
);`}</code></pre>

		<h3 id="sol_curve_validate_point"><a href="#sol_curve_validate_point" class="anchor-wrap"><span class="anchor-hash">#</span>sol_curve_validate_point</a></h3>
		<p>Validates an elliptic curve point. Returns <code>0</code> if the point is valid, otherwise <code>1</code>.</p>
		<p>Supported elliptic curves (specified in r1):</p>
		<ul>
			<li><code>0x00</code>: <em>CURVE25519_EDWARDS</em></li>
			<li><code>0x01</code>: <em>CURVE25519_RISTRETTO</em></li>
		</ul>
		<pre><code class="language-c">{`uint64_t sol_curve_validate_point(
    /* r1 */ int curve_id,
    /* r2 */ const void *point
);`}</code></pre>

		<h3 id="sol_curve_group_op"><a href="#sol_curve_group_op" class="anchor-wrap"><span class="anchor-hash">#</span>sol_curve_group_op</a></h3>
		<p>Provides elliptic curve group operations. Returns <code>0</code> on success, otherwise <code>1</code>.</p>
		<p>See <a href="#sol_curve_validate_point"><code>sol_curve_validate_point</code></a> for supported curves.</p>
		<p>Supported group operations (specified in r2):</p>
		<ul>
			<li><code>0x00</code>: Addition</li>
			<li><code>0x01</code>: Subtraction</li>
			<li><code>0x02</code>: Multiplication</li>
		</ul>
		<p>r3 and r4 point to the left and right input curve points. If the operation was successful, the resulting point gets written to the pointer in r5.</p>
		<pre><code class="language-c">{`uint64_t sol_curve_group_op(
    /* r1 */ int curve_id,
    /* r2 */ int group_op,
    /* r3 */ const void *left_point,
    /* r4 */ const void *right_point,
    /* r5 */ void *result_point
);`}</code></pre>

		<h3 id="sol_get_clock_sysvar"><a href="#sol_get_clock_sysvar" class="anchor-wrap"><span class="anchor-hash">#</span>sol_get_clock_sysvar</a></h3>
		<p>Writes the <a href="https://docs.solana.com/developing/runtime-facilities/sysvars#clock" target="_blank" rel="noopener">clock sysvar</a> to the pointer in r1.</p>
		<pre><code class="language-c">{`typedef struct {
  uint64_t slot;
  int64_t epoch_start_timestamp;
  uint64_t epoch;
  uint64_t leader_schedule_epoch;
  int64_t unix_timestamp;
} SysvarClock;

void sol_get_clock_sysvar(/* r1 */ SysvarClock *);`}</code></pre>

		<h3 id="sol_get_epoch_schedule_sysvar"><a href="#sol_get_epoch_schedule_sysvar" class="anchor-wrap"><span class="anchor-hash">#</span>sol_get_epoch_schedule_sysvar</a></h3>
		<p>Writes the <a href="https://docs.solana.com/developing/runtime-facilities/sysvars#epochschedule" target="_blank" rel="noopener">epoch schedule sysvar</a> to the pointer in r1.</p>
		<pre><code class="language-c">{`typedef struct {
  uint64_t slots_per_epoch;
  uint64_t leader_schedule_slot_offset;
  bool warmup; /* one byte */
  uint64_t first_normal_epoch;
  uint64_t first_normal_slot;
} SysvarEpochSchedule;

void sol_get_epoch_schedule_sysvar(/* r1 */ SysvarEpochSchedule *);`}</code></pre>

		<h3 id="sol_get_fees_sysvar"><a href="#sol_get_fees_sysvar" class="anchor-wrap"><span class="anchor-hash">#</span>sol_get_fees_sysvar</a></h3>
		<p>Writes the <a href="https://docs.solana.com/developing/runtime-facilities/sysvars#fees" target="_blank" rel="noopener">fees sysvar</a> to the pointer in r1.</p>
		<pre><code class="language-c">{`typedef struct {
  uint64_t lamports_per_signature;
} SysvarFees;

void sol_get_fees_sysvar(/* r1 */ SysvarFees *);`}</code></pre>

		<h3 id="sol_get_rent_sysvar"><a href="#sol_get_rent_sysvar" class="anchor-wrap"><span class="anchor-hash">#</span>sol_get_rent_sysvar</a></h3>
		<p>Writes the <a href="https://docs.solana.com/developing/runtime-facilities/sysvars#rent" target="_blank" rel="noopener">rent sysvar</a> to the pointer in r1.</p>
		<pre><code class="language-c">{`typedef struct {
  uint64_t lamports_per_byte_year;
  double exemption_threshold;
  uint8_t burn_percent;
} SysvarRent;

void sol_get_rent_sysvar(/* r1 */ SysvarRent *);`}</code></pre>

		<h3 id="sol_memcpy_"><a href="#sol_memcpy_" class="anchor-wrap"><span class="anchor-hash">#</span>sol_memcpy_</a></h3>
		<p>See <a href="https://man7.org/linux/man-pages/man3/memcpy.3.html" target="_blank" rel="noopener"><code>memcpy(3)</code></a>.</p>
		<p>Throws the <code>SyscallError::CopyOverlapping</code> runtime error if dest and src overlap.</p>
		<pre><code class="language-c">{`void sol_memcpy_(
    /* r1 */ void *restrict dest,
    /* r2 */ const void *restrict src,
    /* r3 */ size_t n
);`}</code></pre>

		<h3 id="sol_memmove_"><a href="#sol_memmove_" class="anchor-wrap"><span class="anchor-hash">#</span>sol_memmove_</a></h3>
		<p>See <a href="https://man7.org/linux/man-pages/man3/memmove.3.html" target="_blank" rel="noopener"><code>memmove(3)</code></a>.</p>
		<pre><code class="language-c">{`void sol_memmove_(
    /* r1 */ void *dest,
    /* r2 */ const void *src,
    /* r3 */ size_t n
);`}</code></pre>

		<h3 id="sol_memcmp_"><a href="#sol_memcmp_" class="anchor-wrap"><span class="anchor-hash">#</span>sol_memcmp_</a></h3>
		<p>See <a href="https://man7.org/linux/man-pages/man3/memcmp.3.html" target="_blank" rel="noopener"><code>memcmp(3)</code></a>.</p>
		<pre><code class="language-c">{`int sol_memcmp_(
    /* r1 */ const void *s1,
    /* r2 */ const void *s2,
    /* r3 */ size_t n
);`}</code></pre>

		<h3 id="sol_memset_"><a href="#sol_memset_" class="anchor-wrap"><span class="anchor-hash">#</span>sol_memset_</a></h3>
		<p>See <a href="https://man7.org/linux/man-pages/man3/memset.3.html" target="_blank" rel="noopener"><code>memset(3)</code></a>.</p>
		<pre><code class="language-c">{`void *sol_memset_(
    /* r1 */ void *s,
    /* r2 */ int c,
    /* r3 */ size_t n
);`}</code></pre>

		<h3 id="sol_invoke_signed_c"><a href="#sol_invoke_signed_c" class="anchor-wrap"><span class="anchor-hash">#</span>sol_invoke_signed_c</a></h3>
		<p>Executes a cross-program invocation (CPI) given a Sealevel instruction, account infos and a list of seed list to derive signers. Each seed list determines one PDA to set as a signer.</p>
		<p>Input <code>r1</code> points to the instruction that will execute the CPI.<br/>
			Input <code>r2</code> points to the account infos array, <code>r3</code> specifies its length.<br/>
			Input <code>r4</code> points to the array of seed lists, <code>r5</code> specifies its length.</p>
		<p>Returns with <code>r0</code> set to zero.</p>
		<pre><code class="language-c">{`#include <sol/cpi.h>

typedef struct {
  SolPubkey *pubkey;
  bool is_writable;
  bool is_signer;
} SolAccountMeta;

typedef struct {
  SolPubkey *program_id;
  SolAccountMeta *accounts;
  uint64_t account_len;
  uint8_t *data;
  uint64_t data_len;
} SolInstruction;

typedef struct {
  SolPubkey *key;
  uint64_t *lamports;
  uint64_t data_len;
  uint8_t *data;
  SolPubkey *owner;
  uint64_t rent_epoch;
  bool is_signer;
  bool is_writable;
  bool executable;
} SolAccountInfo;

typedef struct {
  const uint8_t *seed;
  uint64_t seed_len;
} SolSignerSeed;

typedef struct {
  const SolSignerSeed *seeds;
  uint64_t num_seeds;
} SolSignerSeeds;

void sol_invoke_signed_c(
    /* r1 */ const SolInstruction *instruction,
    /* r2 */ const SolAccountInfo *account_infos,
    /* r3 */ int num_account_infos,
    /* r4 */ const SolSignerSeeds *signers_seeds,
    /* r5 */ int num_signers_seeds
);`}</code></pre>

		<h3 id="sol_invoke_signed_rust"><a href="#sol_invoke_signed_rust" class="anchor-wrap"><span class="anchor-hash">#</span>sol_invoke_signed_rust</a></h3>
		<p>Identical effects to <a href="#sol_invoke_signed_c"><code>sol_invoke_signed_c</code></a> but using the Rust ABI for input parameters. This is the most complex syscall and scheduled to be removed soon. Please refer to the PR below for the reference of this syscall.</p>
		<div class="callout">
			<p>Thanks to <a href="https://robertchen.cc" target="_blank" rel="noopener">Robert Chen</a> at OtterSec for helping me out with CPI entrypoints. They are by far the most complex syscalls.<br/>
				We also fixed some portability/soundness issues with the host implementation of this syscall: <em><a href="https://github.com/solana-labs/solana/pull/26531" target="_blank" rel="noopener">solana-labs/solana#26531</a></em></p>
		</div>

		<h3 id="sol_alloc_free_"><a href="#sol_alloc_free_" class="anchor-wrap"><span class="anchor-hash">#</span>sol_alloc_free_</a></h3>
		<p>Provides memory allocation primitives backed by a bump allocator.</p>
		<p>If <code>r2</code> is a null pointer, allocates a chunk of memory of at least the size in <code>r1</code>. Returns a valid pointer on success or a null pointer on failure.</p>
		<p>If <code>r2</code> is a valid pointer, does nothing.</p>
		<pre><code class="language-c">{`void *sol_alloc_free_(
    /* r1 */ uint64_t size,
    /* r2 */ void *free_addr
);`}</code></pre>

		<h3 id="sol_set_return_data"><a href="#sol_set_return_data" class="anchor-wrap"><span class="anchor-hash">#</span>sol_set_return_data</a></h3>
		<p>Sets the return data of the current instruction. This data can be retrieved with <a href="#sol_get_return_data"><code>sol_get_return_data</code></a> in the parent instruction's context.</p>
		<p>Copies the return data from the pointer in <code>r1</code> with data len given in <code>r2</code> into the return data buffer.</p>
		<pre><code class="language-c">{`#include <sol/return_data.h>
void sol_set_return_data(
    /* r1 */ const void *addr,
    /* r2 */ uint64_t len
);`}</code></pre>

		<h3 id="sol_get_return_data"><a href="#sol_get_return_data" class="anchor-wrap"><span class="anchor-hash">#</span>sol_get_return_data</a></h3>
		<p>Retrieves the return data of the CPI that has last returned back to the current context.</p>
		<p>Copies return data into memory at <code>r1</code> with max data len given in <code>r2</code>. Writes the program ID that set the return data into <code>r3</code>. Returns the number of bytes read, or <code>0</code> if no return data was set.</p>
		<pre><code class="language-c">{`#include <sol/return_data.h>
uint64_t sol_get_return_data(
    /* r1 */ void *return_addr,
    /* r2 */ uint64_t max_len,
    /* r3 */ SolPubkey *program_id
);`}</code></pre>

		<h3 id="sol_log_data"><a href="#sol_log_data" class="anchor-wrap"><span class="anchor-hash">#</span>sol_log_data</a></h3>
		<p>Writes a log entry containing Base64-encoded data to the Sealevel logging facility. The log message uses format <code>Program log: %s</code>. The byte slices pointed to by r1 each get encoded as if they were a contiguous array.</p>
		<p>The compute unit costs are made up from three components:</p>
		<ul>
			<li><code>syscall_base_cost</code> (default <code>100</code>) once per syscall invocation.</li>
			<li><code>syscall_base_cost</code> (default <code>100</code>) times the number of slices.</li>
			<li>One cost unit per byte read in total.</li>
		</ul>
		<pre><code class="language-c">{`#include <sol/log.h>
void sol_log_data(
    /* r1 */ SolBytes *slices,
    /* r2 */ uint64_t num_slices
);`}</code></pre>

		<h3 id="sol_get_processed_sibling_instruction"><a href="#sol_get_processed_sibling_instruction" class="anchor-wrap"><span class="anchor-hash">#</span>sol_get_processed_sibling_instruction</a></h3>
		<p>Copies data of a processed sibling Sealevel instruction to memory. For transaction-level instructions, the list of sibling instructions are the programs that have been invoked previously in the same transaction. Otherwise, it is the list of CPIs that the parent instruction has executed.</p>
		<ul>
			<li><code>r1</code> specifies the instruction index in the list.</li>
			<li><code>r2</code> points to a descriptor of the inputs of the target instruction. As an input param, it specifies the expected instruction data length and number of accounts. After syscall execution, the fields are set to the actual values.</li>
			<li><code>r3</code> is filled in with the program ID invoked by the sibling instruction.</li>
			<li>If the specified data length in <code>r2</code> matches the actual invocation, copies the instruction bytes to <code>r4</code> and the accounts meta list to <code>r5</code>.</li>
		</ul>
		<pre><code class="language-c">{`typedef struct {
  uint64_t data_len;
  uint64_t num_accounts;
} SolProcessedSiblingInstruction;

typedef struct {
  SolPubkey pubkey;
  bool is_signer; /* one byte */
  bool is_writable; /* one byte */
} SolAccountMeta;

bool sol_get_processed_sibling_instruction(
    /* r1 */ int index,
    /* r2 */ SolProcessedSiblingInstruction *meta,
    /* r3 */ SolPubkey *program_id,
    /* r4 */ void *data,
    /* r5 */ SolAccountMeta *accounts
);`}</code></pre>

		<h3 id="sol_get_stack_height"><a href="#sol_get_stack_height" class="anchor-wrap"><span class="anchor-hash">#</span>sol_get_stack_height</a></h3>
		<p>Returns the height of the Sealevel invocation stack, which is <code>1</code> at transaction level and increases for every cross-program invocation. Note that this is unrelated to the SBF call stack.</p>
		<pre><code class="language-c">{`uint64_t sol_get_stack_height();`}</code></pre>
	</div>

	<nav class="article-nav">
		<a href="/sol-0x03-isa" class="nav-prev">
			&larr; Previous: 0x03: SBF Instruction Set
		</a>
		<div></div>
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
		min-width: 500px;
	}

	.callout {
		background: #f3f4f6;
		border-left: 4px solid #22d3ee;
		padding: 1rem 1.25rem;
		margin: 1.5rem 0;
		border-radius: 0 8px 8px 0;
	}

	.callout p {
		margin: 0;
		font-size: 0.95rem;
	}

	.article-nav {
		display: flex;
		justify-content: space-between;
		margin-top: 3rem;
		padding-top: 1.5rem;
		border-top: 2px solid #e5e7eb;
	}

	.nav-prev {
		font-family: 'Inter', system-ui, sans-serif;
		font-weight: 600;
		font-size: 0.95rem;
	}
</style>
