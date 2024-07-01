# Node.js 20 ChangeLog

<!--lint disable maximum-line-length no-literal-urls prohibited-strings-->

<table>
<tr>
<th>LTS 'Iron'</th>
<th>Current</th>
</tr>
<tr>
<td>
<a href="#20.15.0">20.15.0</a><br/>
<a href="#20.14.0">20.14.0</a><br/>
<a href="#20.13.1">20.13.1</a><br/>
<a href="#20.13.0">20.13.0</a><br/>
<a href="#20.12.2">20.12.2</a><br/>
<a href="#20.12.1">20.12.1</a><br/>
<a href="#20.12.0">20.12.0</a><br/>
<a href="#20.11.1">20.11.1</a><br/>
<a href="#20.11.0">20.11.0</a><br/>
<a href="#20.10.0">20.10.0</a><br/>
<a href="#20.9.0">20.9.0</a><br/>
</td>
<td>
<a href="#20.8.1">20.8.1</a><br/>
<a href="#20.8.0">20.8.0</a><br/>
<a href="#20.7.0">20.7.0</a><br/>
<a href="#20.6.1">20.6.1</a><br/>
<a href="#20.6.0">20.6.0</a><br/>
<a href="#20.5.1">20.5.1</a><br/>
<a href="#20.5.0">20.5.0</a><br/>
<a href="#20.4.0">20.4.0</a><br/>
<a href="#20.3.1">20.3.1</a><br/>
<a href="#20.3.0">20.3.0</a><br/>
<a href="#20.2.0">20.2.0</a><br/>
<a href="#20.1.0">20.1.0</a><br/>
<a href="#20.0.0">20.0.0</a><br/>
</td>
</tr>
</table>

* Other Versions
  * [19.x](CHANGELOG_V19.md)
  * [18.x](CHANGELOG_V18.md)
  * [17.x](CHANGELOG_V17.md)
  * [16.x](CHANGELOG_V16.md)
  * [15.x](CHANGELOG_V15.md)
  * [14.x](CHANGELOG_V14.md)
  * [13.x](CHANGELOG_V13.md)
  * [12.x](CHANGELOG_V12.md)
  * [11.x](CHANGELOG_V11.md)
  * [10.x](CHANGELOG_V10.md)
  * [9.x](CHANGELOG_V9.md)
  * [8.x](CHANGELOG_V8.md)
  * [7.x](CHANGELOG_V7.md)
  * [6.x](CHANGELOG_V6.md)
  * [5.x](CHANGELOG_V5.md)
  * [4.x](CHANGELOG_V4.md)
  * [0.12.x](CHANGELOG_V012.md)
  * [0.10.x](CHANGELOG_V010.md)
  * [io.js](CHANGELOG_IOJS.md)
  * [Archive](CHANGELOG_ARCHIVE.md)

<a id="20.15.0"></a>

## 2024-06-20, Version 20.15.0 'Iron' (LTS), @marco-ippolito

### test\_runner: support test plans

It is now possible to count the number of assertions and subtests that are expected to run within a test. If the number of assertions and subtests that run does not match the expected count, the test will fail.

```js
test('top level test', (t) => {
  t.plan(2);
  t.assert.ok('some relevant assertion here');
  t.subtest('subtest', () => {});
});
```

Contributed by Colin Ihrig in [#52860](https://github.com/nodejs/node/pull/52860)

### inspector: introduce the `--inspect-wait` flag

This release introduces the `--inspect-wait` flag, which allows debugger to wait for attachement. This flag is useful when you want to debug the code from the beginning. Unlike `--inspect-brk`, which breaks on the first line, this flag waits for debugger to be connected and then runs the code as soon as a session is established.

Contributed by Kohei Ueno in [#52734](https://github.com/nodejs/node/pull/52734)

### zlib: expose zlib.crc32()

This release exposes the crc32() function from zlib to user-land.

It computes a 32-bit Cyclic Redundancy Check checksum of data. If
value is specified, it is used as the starting value of the checksum,
otherwise, 0 is used as the starting value.

The CRC algorithm is designed to compute checksums and to detect error
in data transmission. It's not suitable for cryptographic authentication.

```js
const zlib = require('node:zlib');
const { Buffer } = require('node:buffer');

let crc = zlib.crc32('hello');  // 907060870
crc = zlib.crc32('world', crc);  // 4192936109

crc = zlib.crc32(Buffer.from('hello', 'utf16le'));  // 1427272415
crc = zlib.crc32(Buffer.from('world', 'utf16le'), crc);  // 4150509955
```

Contributed by Joyee Cheung in [#52692](https://github.com/nodejs/node/pull/52692)

### cli: allow running wasm in limited vmem with --disable-wasm-trap-handler

By default, Node.js enables trap-handler-based WebAssembly bound
checks. As a result, V8 does not need to insert inline bound checks
int the code compiled from WebAssembly which may speedup WebAssembly
execution significantly, but this optimization requires allocating
a big virtual memory cage (currently 10GB). If the Node.js process
does not have access to a large enough virtual memory address space
due to system configurations or hardware limitations, users won't
be able to run any WebAssembly that involves allocation in this
virtual memory cage and will see an out-of-memory error.

```console
$ ulimit -v 5000000
$ node -p "new WebAssembly.Memory({ initial: 10, maximum: 100 });"
[eval]:1
new WebAssembly.Memory({ initial: 10, maximum: 100 });
^

RangeError: WebAssembly.Memory(): could not allocate memory
    at [eval]:1:1
    at runScriptInThisContext (node:internal/vm:209:10)
    at node:internal/process/execution:118:14
    at [eval]-wrapper:6:24
    at runScript (node:internal/process/execution:101:62)
    at evalScript (node:internal/process/execution:136:3)
    at node:internal/main/eval_string:49:3

```

`--disable-wasm-trap-handler` disables this optimization so that
users can at least run WebAssembly (with a less optimial performance)
when the virtual memory address space available to their Node.js
process is lower than what the V8 WebAssembly memory cage needs.

Contributed by Joyee Cheung in [#52766](https://github.com/nodejs/node/pull/52766)

### Other Notable Changes

* \[[`12512c3d0e`](https://github.com/nodejs/node/commit/12512c3d0e)] - **doc**: add pimterry to collaborators (Tim Perry) [#52874](https://github.com/nodejs/node/pull/52874)
* \[[`9d485b40bb`](https://github.com/nodejs/node/commit/9d485b40bb)] - **(SEMVER-MINOR)** **tools**: fix get\_asan\_state() in tools/test.py (Joyee Cheung) [#52766](https://github.com/nodejs/node/pull/52766)
* \[[`e98c305f52`](https://github.com/nodejs/node/commit/e98c305f52)] - **(SEMVER-MINOR)** **tools**: support max\_virtual\_memory test configuration (Joyee Cheung) [#52766](https://github.com/nodejs/node/pull/52766)
* \[[`dce0300896`](https://github.com/nodejs/node/commit/dce0300896)] - **(SEMVER-MINOR)** **tools**: support != in test status files (Joyee Cheung) [#52766](https://github.com/nodejs/node/pull/52766)

### Commits

* \[[`227093bfec`](https://github.com/nodejs/node/commit/227093bfec)] - **assert**: add deep equal check for more Error type (Zhenwei Jin) [#51805](https://github.com/nodejs/node/pull/51805)
* \[[`184cfe5a71`](https://github.com/nodejs/node/commit/184cfe5a71)] - **benchmark**: filter non-present deps from `start-cli-version` (Adam Majer) [#51746](https://github.com/nodejs/node/pull/51746)
* \[[`8b3e83bb53`](https://github.com/nodejs/node/commit/8b3e83bb53)] - **buffer**: even faster atob (Daniel Lemire) [#52443](https://github.com/nodejs/node/pull/52443)
* \[[`8d628c3255`](https://github.com/nodejs/node/commit/8d628c3255)] - **buffer**: use size\_t instead of uint32\_t to avoid segmentation fault (Xavier Stouder) [#48033](https://github.com/nodejs/node/pull/48033)
* \[[`16ae2b2933`](https://github.com/nodejs/node/commit/16ae2b2933)] - **buffer**: remove lines setting indexes to integer value (Zhenwei Jin) [#52588](https://github.com/nodejs/node/pull/52588)
* \[[`48c15d0dcd`](https://github.com/nodejs/node/commit/48c15d0dcd)] - **build**: remove deprecated calls for argument groups (Mohammed Keyvanzadeh) [#52913](https://github.com/nodejs/node/pull/52913)
* \[[`1be8232d17`](https://github.com/nodejs/node/commit/1be8232d17)] - **build**: drop base64 dep in GN build (Cheng) [#52856](https://github.com/nodejs/node/pull/52856)
* \[[`918962d6e7`](https://github.com/nodejs/node/commit/918962d6e7)] - **build**: make simdjson a public dep in GN build (Cheng) [#52755](https://github.com/nodejs/node/pull/52755)
* \[[`5215b6fd8e`](https://github.com/nodejs/node/commit/5215b6fd8e)] - **build, tools**: copy release assets to staging R2 bucket once built (flakey5) [#51394](https://github.com/nodejs/node/pull/51394)
* \[[`473fa73857`](https://github.com/nodejs/node/commit/473fa73857)] - **(SEMVER-MINOR)** **cli**: allow running wasm in limited vmem with --disable-wasm-trap-handler (Joyee Cheung) [#52766](https://github.com/nodejs/node/pull/52766)
* \[[`954d2aded4`](https://github.com/nodejs/node/commit/954d2aded4)] - **cluster**: replace `forEach` with `for-of` loop (Jérôme Benoit) [#50317](https://github.com/nodejs/node/pull/50317)
* \[[`794e450ea7`](https://github.com/nodejs/node/commit/794e450ea7)] - **console**: colorize console error and warn (Jithil P Ponnan) [#51629](https://github.com/nodejs/node/pull/51629)
* \[[`0fb7c18f10`](https://github.com/nodejs/node/commit/0fb7c18f10)] - **crypto**: fix duplicated switch-case return values (Mustafa Ateş UZUN) [#49030](https://github.com/nodejs/node/pull/49030)
* \[[`cd1415c8b2`](https://github.com/nodejs/node/commit/cd1415c8b2)] - _**Revert**_ "**crypto**: make timingSafeEqual faster for Uint8Array" (Tobias Nießen) [#53390](https://github.com/nodejs/node/pull/53390)
* \[[`b774544bb1`](https://github.com/nodejs/node/commit/b774544bb1)] - **deps**: enable unbundling of simdjson, simdutf, ada (Daniel Lemire) [#52924](https://github.com/nodejs/node/pull/52924)
* \[[`da4dbfc5fd`](https://github.com/nodejs/node/commit/da4dbfc5fd)] - **doc**: remove reference to AUTHORS file (Marco Ippolito) [#52960](https://github.com/nodejs/node/pull/52960)
* \[[`2f3f2ff8af`](https://github.com/nodejs/node/commit/2f3f2ff8af)] - **doc**: update hljs with the latest styles (Aviv Keller) [#52911](https://github.com/nodejs/node/pull/52911)
* \[[`3a1d17a9b1`](https://github.com/nodejs/node/commit/3a1d17a9b1)] - **doc**: mention quicker way to build docs (Alex Crawford) [#52937](https://github.com/nodejs/node/pull/52937)
* \[[`be309bd19d`](https://github.com/nodejs/node/commit/be309bd19d)] - **doc**: mention push.followTags config (Rafael Gonzaga) [#52906](https://github.com/nodejs/node/pull/52906)
* \[[`e62c6e2684`](https://github.com/nodejs/node/commit/e62c6e2684)] - **doc**: document pipeline with `end` option (Alois Klink) [#48970](https://github.com/nodejs/node/pull/48970)
* \[[`af27225cf6`](https://github.com/nodejs/node/commit/af27225cf6)] - **doc**: add example for `execFileSync` method and ref to stdio (Evan Shortiss) [#39412](https://github.com/nodejs/node/pull/39412)
* \[[`086626f9b1`](https://github.com/nodejs/node/commit/086626f9b1)] - **doc**: add examples and notes to http server.close et al (mary marchini) [#49091](https://github.com/nodejs/node/pull/49091)
* \[[`3aa3337a00`](https://github.com/nodejs/node/commit/3aa3337a00)] - **doc**: fix `dns.lookup` family `0` and `all` descriptions (Adam Jones) [#51653](https://github.com/nodejs/node/pull/51653)
* \[[`585f2a2e7f`](https://github.com/nodejs/node/commit/585f2a2e7f)] - **doc**: update `fs.realpath` documentation (sinkhaha) [#48170](https://github.com/nodejs/node/pull/48170)
* \[[`4bf3d44e1d`](https://github.com/nodejs/node/commit/4bf3d44e1d)] - **doc**: update fs read documentation for clarity (Mert Can Altin) [#52453](https://github.com/nodejs/node/pull/52453)
* \[[`ae5d47dde3`](https://github.com/nodejs/node/commit/ae5d47dde3)] - **doc**: watermark string behavior (Benjamin Gruenbaum) [#52842](https://github.com/nodejs/node/pull/52842)
* \[[`1e429d10d3`](https://github.com/nodejs/node/commit/1e429d10d3)] - **doc**: exclude commits with baking-for-lts (Marco Ippolito) [#52896](https://github.com/nodejs/node/pull/52896)
* \[[`3df3e37cdb`](https://github.com/nodejs/node/commit/3df3e37cdb)] - **doc**: add names next to release key bash commands (Aviv Keller) [#52878](https://github.com/nodejs/node/pull/52878)
* \[[`12512c3d0e`](https://github.com/nodejs/node/commit/12512c3d0e)] - **doc**: add pimterry to collaborators (Tim Perry) [#52874](https://github.com/nodejs/node/pull/52874)
* \[[`97e0fef019`](https://github.com/nodejs/node/commit/97e0fef019)] - **doc**: add more definitions to GLOSSARY.md (Aviv Keller) [#52798](https://github.com/nodejs/node/pull/52798)
* \[[`91fadac162`](https://github.com/nodejs/node/commit/91fadac162)] - **doc**: make docs more welcoming and descriptive for newcomers (Serkan Özel) [#38056](https://github.com/nodejs/node/pull/38056)
* \[[`a3b20126fd`](https://github.com/nodejs/node/commit/a3b20126fd)] - **doc**: add OpenSSL errors to API docs (John Lamp) [#34213](https://github.com/nodejs/node/pull/34213)
* \[[`9587ae9b5b`](https://github.com/nodejs/node/commit/9587ae9b5b)] - **doc**: simplify copy-pasting of `branch-diff` commands (Antoine du Hamel) [#52757](https://github.com/nodejs/node/pull/52757)
* \[[`6ea72a53c3`](https://github.com/nodejs/node/commit/6ea72a53c3)] - **doc**: add test\_runner to subsystem (Raz Luvaton) [#52774](https://github.com/nodejs/node/pull/52774)
* \[[`972eafd983`](https://github.com/nodejs/node/commit/972eafd983)] - **events**: update MaxListenersExceededWarning message log (sinkhaha) [#51921](https://github.com/nodejs/node/pull/51921)
* \[[`74753ed1fe`](https://github.com/nodejs/node/commit/74753ed1fe)] - **events**: add stop propagation flag to `Event.stopImmediatePropagation` (Mickael Meausoone) [#39463](https://github.com/nodejs/node/pull/39463)
* \[[`75dd009649`](https://github.com/nodejs/node/commit/75dd009649)] - **events**: replace NodeCustomEvent with CustomEvent (Feng Yu) [#43876](https://github.com/nodejs/node/pull/43876)
* \[[`7d38c2e012`](https://github.com/nodejs/node/commit/7d38c2e012)] - **fs**: keep fs.promises.readFile read until EOF is reached (Zhenwei Jin) [#52178](https://github.com/nodejs/node/pull/52178)
* \[[`8cb13120d3`](https://github.com/nodejs/node/commit/8cb13120d3)] - **(SEMVER-MINOR)** **inspector**: introduce the `--inspect-wait` flag (Kohei Ueno) [#52734](https://github.com/nodejs/node/pull/52734)
* \[[`d5ab1de1fd`](https://github.com/nodejs/node/commit/d5ab1de1fd)] - **meta**: move `@anonrig` to TSC regular member (Yagiz Nizipli) [#52932](https://github.com/nodejs/node/pull/52932)
* \[[`f82d086e90`](https://github.com/nodejs/node/commit/f82d086e90)] - **path**: fix toNamespacedPath on Windows (Hüseyin Açacak) [#52915](https://github.com/nodejs/node/pull/52915)
* \[[`121ea13b50`](https://github.com/nodejs/node/commit/121ea13b50)] - **process**: improve event-loop (Aras Abbasi) [#52108](https://github.com/nodejs/node/pull/52108)
* \[[`eceac784aa`](https://github.com/nodejs/node/commit/eceac784aa)] - **repl**: fix disruptive autocomplete without inspector (Nitzan Uziely) [#40661](https://github.com/nodejs/node/pull/40661)
* \[[`89a910be82`](https://github.com/nodejs/node/commit/89a910be82)] - **src**: fix Worker termination in `inspector.waitForDebugger` (Daeyeon Jeong) [#52527](https://github.com/nodejs/node/pull/52527)
* \[[`033f985e8a`](https://github.com/nodejs/node/commit/033f985e8a)] - **src**: use `S_ISDIR` to check if the file is a directory (theanarkh) [#52164](https://github.com/nodejs/node/pull/52164)
* \[[`95128399f8`](https://github.com/nodejs/node/commit/95128399f8)] - **src**: allow preventing debug signal handler start (Shelley Vohr) [#46681](https://github.com/nodejs/node/pull/46681)
* \[[`b162aeae9e`](https://github.com/nodejs/node/commit/b162aeae9e)] - **src**: fix typo Unabled -> Unable (Simon Siefke) [#52820](https://github.com/nodejs/node/pull/52820)
* \[[`2dcbf1894a`](https://github.com/nodejs/node/commit/2dcbf1894a)] - **src**: avoid unused variable 'error' warning (Michaël Zasso) [#52886](https://github.com/nodejs/node/pull/52886)
* \[[`978ee0a635`](https://github.com/nodejs/node/commit/978ee0a635)] - **src**: only apply fix in main thread (Paolo Insogna) [#52702](https://github.com/nodejs/node/pull/52702)
* \[[`8fc52b38c6`](https://github.com/nodejs/node/commit/8fc52b38c6)] - **src**: fix test local edge case (Paolo Insogna) [#52702](https://github.com/nodejs/node/pull/52702)
* \[[`d02907ecc4`](https://github.com/nodejs/node/commit/d02907ecc4)] - **src**: remove misplaced windows code under posix guard in node.cc (Ali Hassan) [#52545](https://github.com/nodejs/node/pull/52545)
* \[[`af29120fa7`](https://github.com/nodejs/node/commit/af29120fa7)] - **stream**: use `ByteLengthQueuingStrategy` when not in `objectMode` (Jason) [#48847](https://github.com/nodejs/node/pull/48847)
* \[[`a5f3dd137c`](https://github.com/nodejs/node/commit/a5f3dd137c)] - **string\_decoder**: throw an error when writing a too long buffer (zhenweijin) [#52215](https://github.com/nodejs/node/pull/52215)
* \[[`65fa95d57d`](https://github.com/nodejs/node/commit/65fa95d57d)] - **test**: add `Debugger.setInstrumentationBreakpoint` known issue (Konstantin Ulitin) [#31137](https://github.com/nodejs/node/pull/31137)
* \[[`0513e07805`](https://github.com/nodejs/node/commit/0513e07805)] - **test**: use `for-of` instead of `forEach` (Gibby Free) [#49790](https://github.com/nodejs/node/pull/49790)
* \[[`1d01325928`](https://github.com/nodejs/node/commit/1d01325928)] - **test**: verify request payload is uploaded consistently (Austin Wright) [#34066](https://github.com/nodejs/node/pull/34066)
* \[[`7dda156872`](https://github.com/nodejs/node/commit/7dda156872)] - **test**: add fuzzer for native/js string conversion (Adam Korczynski) [#51120](https://github.com/nodejs/node/pull/51120)
* \[[`5fb829b340`](https://github.com/nodejs/node/commit/5fb829b340)] - **test**: add fuzzer for `ClientHelloParser` (AdamKorcz) [#51088](https://github.com/nodejs/node/pull/51088)
* \[[`cc74bf789f`](https://github.com/nodejs/node/commit/cc74bf789f)] - **test**: fix broken env fuzzer by initializing process (AdamKorcz) [#51080](https://github.com/nodejs/node/pull/51080)
* \[[`800b6f65cf`](https://github.com/nodejs/node/commit/800b6f65cf)] - **test**: replace `forEach()` in `test-stream-pipe-unpipe-stream` (Dario) [#50786](https://github.com/nodejs/node/pull/50786)
* \[[`d08c9a6a31`](https://github.com/nodejs/node/commit/d08c9a6a31)] - **test**: test pipeline `end` on transform streams (Alois Klink) [#48970](https://github.com/nodejs/node/pull/48970)
* \[[`0be8123ede`](https://github.com/nodejs/node/commit/0be8123ede)] - **test**: improve coverage of lib/readline.js (Rongjian Zhang) [#38646](https://github.com/nodejs/node/pull/38646)
* \[[`410224415c`](https://github.com/nodejs/node/commit/410224415c)] - **test**: updated for each to for of in test file (lyannel) [#50308](https://github.com/nodejs/node/pull/50308)
* \[[`556e9a2127`](https://github.com/nodejs/node/commit/556e9a2127)] - **test**: move `test-http-server-request-timeouts-mixed` to sequential (Madhuri) [#45722](https://github.com/nodejs/node/pull/45722)
* \[[`0638274c07`](https://github.com/nodejs/node/commit/0638274c07)] - **test**: fix DNS cancel tests (Szymon Marczak) [#44432](https://github.com/nodejs/node/pull/44432)
* \[[`311bdc62bd`](https://github.com/nodejs/node/commit/311bdc62bd)] - **test**: add http agent to `executionAsyncResource` (psj-tar-gz) [#34966](https://github.com/nodejs/node/pull/34966)
* \[[`6001b164ab`](https://github.com/nodejs/node/commit/6001b164ab)] - **test**: reduce memory usage of test-worker-stdio (Adam Majer) [#37769](https://github.com/nodejs/node/pull/37769)
* \[[`986bfa26e9`](https://github.com/nodejs/node/commit/986bfa26e9)] - **test**: add common.expectRequiredModule() (Joyee Cheung) [#52868](https://github.com/nodejs/node/pull/52868)
* \[[`2246d4fd1e`](https://github.com/nodejs/node/commit/2246d4fd1e)] - **test**: crypto-rsa-dsa testing for dynamic openssl (Michael Dawson) [#52781](https://github.com/nodejs/node/pull/52781)
* \[[`1dce5dea0b`](https://github.com/nodejs/node/commit/1dce5dea0b)] - **test**: skip some console tests on dumb terminal (Adam Majer) [#37770](https://github.com/nodejs/node/pull/37770)
* \[[`0addeb240c`](https://github.com/nodejs/node/commit/0addeb240c)] - **test**: skip v8-updates/test-linux-perf-logger (Michaël Zasso) [#52821](https://github.com/nodejs/node/pull/52821)
* \[[`56e19e38f3`](https://github.com/nodejs/node/commit/56e19e38f3)] - **test**: drop test-crypto-timing-safe-equal-benchmarks (Rafael Gonzaga) [#52751](https://github.com/nodejs/node/pull/52751)
* \[[`0c5e58958c`](https://github.com/nodejs/node/commit/0c5e58958c)] - **test, crypto**: use correct object on assert (响马) [#51820](https://github.com/nodejs/node/pull/51820)
* \[[`d54aa47ec1`](https://github.com/nodejs/node/commit/d54aa47ec1)] - **(SEMVER-MINOR)** **test\_runner**: support test plans (Colin Ihrig) [#52860](https://gith