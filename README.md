![Banner](banner.png)

# crypto-post-quantum-kem-tester

A command line harness that exercises a Key Encapsulation Mechanism (KEM) interface: repeated encapsulate and decapsulate cycles with timing, an optional AES-256-CBC encryption round trip using a key derived from the KEM shared secret via HKDF-SHA256, and X25519 key pair generation to PEM files.

Important limitation: the only KEM implemented is `DummyKEM`, a placeholder that implements the KEM interface using an ephemeral X25519 Diffie-Hellman exchange. X25519 is a classical algorithm, not a post-quantum KEM. No real post-quantum KEM (for example ML-KEM/Kyber) is implemented, and passing another name to `--kem_type` fails with an unsupported KEM error. This tool tests integration and round trip correctness of the KEM interface, not post-quantum security.

## Requirements

- Python 3.8 or later
- The `cryptography` package, version 41.0.0 or later (see `requirements.txt`)

## Install

```bash
git clone https://github.com/ShadowGuardAI/crypto-post-quantum-kem-tester.git
cd crypto-post-quantum-kem-tester
pip install -r requirements.txt
```

## Usage

```bash
python3 main.py [--iterations N] [--test_encryption] [--message_size BYTES]
                [--kem_type DummyKEM] [--cipher AES256]
                [--generate_keys --public_key_file PUB --private_key_file PRIV]
```

The exit code is 0 when every requested test passes and 1 when any requested test or operation fails.

### Parameters

- `-h`, `--help`: Show help message and exit.
- `--iterations`: Number of encapsulate/decapsulate iterations in the performance test (default 10). Each iteration checks that decapsulation reproduces the encapsulated shared secret.
- `--test_encryption`: Also test encryption and decryption of a random message with a key derived from the KEM shared secret.
- `--message_size`: Size in bytes of the random test message used with `--test_encryption` (default 1024).
- `--kem_type`: KEM to use (default `DummyKEM`). Only `DummyKEM` is implemented.
- `--cipher`: Cipher for the encryption test (default `AES256`). Only `AES256` (AES-256-CBC with PKCS7 padding) is supported; any other value fails.
- `--public_key_file`: Output file for the generated public key. Required with `--generate_keys`.
- `--private_key_file`: Output file for the generated private key. Required with `--generate_keys`.
- `--generate_keys`: Generate an X25519 key pair for `DummyKEM` and write it as PEM files (public key in SubjectPublicKeyInfo form, private key in unencrypted PKCS8 form), then exit. The key files are outputs only; no mode reads key files back for encapsulation or decapsulation.

## Examples

Performance test only:

```bash
python3 main.py --iterations 10
```

Verified output:

```text
INFO - KEM performance test completed in 0.0477 seconds for 10 iterations.
INFO - Average time per iteration: 0.004770 seconds.
```

Performance plus encryption round trip:

```bash
python3 main.py --iterations 5 --test_encryption --message_size 2048
```

Verified output ends with:

```text
INFO - Encryption/Decryption test successful.
```

Generate a key pair:

```bash
python3 main.py --generate_keys --public_key_file pub.pem --private_key_file priv.pem
```

## License

Copyright (c) ShadowGuardAI
