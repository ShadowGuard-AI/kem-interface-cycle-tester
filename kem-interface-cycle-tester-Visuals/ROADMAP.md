# KEM interface cycle tester roadmap

Written 2026-10-30 from the repo as it stands. No dates on the
future items, because I do not know when I will be back in this
code, and a date I invent here would be a small lie in a public
file.

## Done

- DummyKEM behind the KEM interface: an X25519 key pair,
  encapsulate with a fresh ephemeral key whose public half is
  the ciphertext, and decapsulate landing on the same shared
  secret.
- The cycle test: repeated encapsulate and decapsulate with a
  secret comparison on every cycle and timing in the log,
  10 iterations by default.
- The encryption round trip: HKDF-SHA256 from the shared
  secret, AES-256-CBC with PKCS7 padding, and a byte for byte
  comparison at the end.
- X25519 key pair generation to PEM files, and up-front
  validation of the KEM name, the cipher, and the message
  size in every mode, with exit codes that match the result.

## Working now

- Nothing in flight. The repo holds no TODO or FIXME notes
  (I searched the tracked files on 2026-10-30) and no test
  suite.

## Next

- A test suite. The repo ships none, and the round trip
  promises here, same secret every cycle and same message
  after decryption, are exactly what tests should pin down.
- A recorded run in an environment with the cryptography
  package installed, so the timing and pass lines rest on a
  fresh run and not only on the README's earlier record.

## Later

- A real post-quantum KEM behind this same interface. The
  code comments point that way in their own words, calling
  DummyKEM a placeholder to replace when actual KEMs are
  available. That is a direction recorded in the code, not a
  scheduled plan, and nothing in this repo is post-quantum
  until that work exists and is tested as itself.

## Not planned

- Calling DummyKEM, or anything built on X25519, post-quantum.
  It is a classical exchange and the README says so in its
  second paragraph; that sentence does not move.
- Reading generated key files back into a test run. The key
  files are outputs, and keeping them one-way keeps the
  harness simple and its claims small.
