# KEM interface cycle tester, drawn out

I made these five pictures because the honest thing about this
repo is easy to miss in prose: it exercises a Key Encapsulation
Mechanism interface, and the only implementation behind that
interface is a classical placeholder. Everything in them comes
from the one file that does the work, main.py, and from the
README beside it.

## The visuals

- [architecture.svg](architecture.svg): the system shape. The
  DummyKEM class panel on the left, the test and helper
  functions on the right, and main() underneath as the gate
  that validates the named choices before any mode runs.
- [sequence.svg](sequence.svg): one cycle drawn as a sequence.
  Four lifelines: the caller, DummyKEM, the fresh ephemeral
  X25519 key, and the shared secret both sides land on.
- [flowchart.svg](flowchart.svg): a run, followed as decisions.
  Validation first, then the key generation branch, then the
  timed cycles, then the optional encryption test, then an exit
  code that matches what actually passed.
- [product-map.svg](product-map.svg): the product as three
  bands and a boundary: who it serves and why, what it does,
  what goes in and comes out, and the plain statement that the
  placeholder is not post-quantum.

- [statistics.svg](statistics.svg): the numbers from the table below, drawn as bars on paper: lines by kind, with the file and commit counts noted under them.

Each sheet also ships as a PNG beside its SVG (architecture.png,
flowchart.png, and so on): the same drawing, finished as a
photograph of the paper page.

Every sheet here is drawn to look like what it is: a page sketched
by hand on paper, and each one is signed GK somewhere in the
drawing, never twice in the same place, because that is what I do
with a page I made.

## Written product map

What it is: a command line harness that exercises a KEM
interface: repeated encapsulate and decapsulate cycles with
timing, an optional AES-256-CBC encryption round trip using a
key derived from the shared secret via HKDF-SHA256, and X25519
key pair generation to PEM files.

Who it serves: me, checking that a KEM-shaped interface round
trips correctly before any real implementation is trusted
behind it, and anyone testing the same integration shape.

Capabilities, grouped:

- The interface under test: DummyKEM holds one X25519 key pair.
  encapsulate makes a fresh ephemeral X25519 key, runs the
  exchange against the public key, and returns the ephemeral
  public key as the ciphertext with the shared secret.
  decapsulate runs the exchange with the private key and that
  ephemeral public key, landing on the same secret.
- The cycle test: test_kem_performance repeats the pair of
  calls for the requested iterations (10 by default), compares
  the two secrets on every cycle, and logs the total and
  average time.
- The encryption round trip: derive_key runs HKDF-SHA256 over
  the shared secret, encrypt_message and decrypt_message carry
  a random message through AES-256-CBC with PKCS7 padding, and
  the test passes only when the decrypted bytes match.
- Key generation: generate_key_pair writes an X25519 pair as
  PEM files, public in SubjectPublicKeyInfo form and private in
  unencrypted PKCS8 form, then the run exits.
- Up-front validation: main refuses an unsupported kem_type or
  cipher, and a message size under 1 with the encryption test,
  before any mode runs. The exit code is 0 only when every
  requested test passes.

Inputs: command line flags only: iterations, the encryption
switch and message size, the KEM and cipher names, and the two
key file paths for generation. Outputs: log lines with timing
and results, the two PEM files when generation is asked for,
and the exit code.

Boundaries, what it does NOT do: the only KEM implemented is
DummyKEM, a placeholder built on an ephemeral X25519 exchange.
X25519 is a classical algorithm, not a post-quantum KEM. No real
post-quantum KEM, for example ML-KEM, is implemented here, and
any other name given to --kem_type fails with an unsupported
KEM error. The generated key files are outputs only; no mode
reads key files back for encapsulation or decapsulation. This
tool tests integration and round trip correctness of the
interface, not post-quantum security.

## Statistics, with citations

Counted 2026-10-30, from the clone this folder ships in, at the
commit before this visuals commit (d0f4efdbe6a8c9f62929e9427cade1e3b285654a).

| Number | Value | How it was counted, and from what |
|---|---|---|
| Files in the repo | 6 | git ls-files in the clone, counted 2026-10-30 |
| Python lines | 280 | Line count over the 1 tracked .py file (main.py), by wc -l, binary and image files excluded, 2026-10-30 |
| Markdown lines | 79 | Line count over the 1 tracked .md file (README.md), same method, 2026-10-30 |
| License lines | 21 | Line count over the tracked LICENSE file, same method, 2026-10-30 |
| requirements.txt lines | 1 | wc -l on the tracked requirements.txt (cryptography>=41.0.0), 2026-10-30 |
| Commits | 9 | git log in the clone, counted 2026-10-30 |
| First commit | 2025-07-22 | git log, oldest entry, 2026-10-30 |
| Last commit before this one | 2026-10-30 | git log, newest entry, 2026-10-30 |
| Tests | No test suite in the repo | No test files exist among the tracked files (git ls-files, 2026-10-30), so no tests were run and no pass is claimed |
| Run state in this campaign | Not run | The tool needs the cryptography package, which is not installed in the machine this was counted on, and I did not install anything for a documentation pass; a hand run on 2026-10-30 stopped at the import. The README carries verified output from an earlier run, which I cite as the README's record, not as my run |
| Languages (API) | Python 11,179 bytes | GitHub API, GET /repos/ShadowGuard-AI/kem-interface-cycle-tester/languages, read 2026-10-30 |
| Repo size (API) | 141 KB | GitHub API, GET /repos/ShadowGuard-AI/kem-interface-cycle-tester, size field, read 2026-10-30 |
| Stars / forks (API) | 1 / 0 | Same API response, read 2026-10-30 |

External statistics: none. No outside number says anything honest
about this repo, so the repo's own numbers are the whole table.

## Roadmap summary

Done: the cycle test, the encryption round trip, key generation,
and up-front validation in every mode. Working now: nothing in
flight. Next, if I return to it: a test suite, since the repo
ships none. Later: a real KEM behind the same interface is the
direction the code comments point at, and it is recorded there
as a direction, not a plan with a date. The full roadmap, with
the Later and Not planned sections, is in [ROADMAP.md](ROADMAP.md).
