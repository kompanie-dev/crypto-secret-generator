# Crypto Secret Generator

A single-page web app for generating random secrets, API keys, and license-code-style strings. Everything — markup, styles, and logic — lives in one dependency-free `index.html`; just open it in a browser.

## Features

- **Cryptographically secure randomness** — uses the Web Crypto API (`crypto.getRandomValues`) with rejection sampling to avoid modulo bias, so every allowed character is equally likely.
- **Block formatting** — secrets are generated as N blocks of M characters, joined by hyphens (e.g. `a712x9-f3p8q1-z5m4c2`).
- **Configurable options**
  - Block length (1–10) and number of blocks (1–10)
  - Custom allowed character set
  - Uppercase the first block (handy for license keys)
- **Presets** — one-click setups for Letters + Numbers, Letters, or Numbers.
- **Entropy display** — live estimate in bits (length × log₂ of the character-set size).
- **Clipboard copy** — with visual feedback ("Copied!"/"Copy failed!").
- **Show/hide secret** — toggle between plain text and password masking.
- **Keyboard shortcuts**
  - `Alt+X` — generate
  - `Ctrl+C` / `Cmd+C` — copy (unless typing in another field)
  - `Alt+H` — show/hide the secret
- **Live regeneration** — changing any option regenerates the secret immediately; numeric inputs are clamped to their min/max.

## Code overview

The script in `index.html` is organized around a few small functions:

- `generateCryptoRandomString(length, allowedCharacters)` — fills a `Uint32Array` with random values via `crypto.getRandomValues` and maps them to characters, discarding values at or above the bias-free cutoff (`floor(2³² / alphabetSize) × alphabetSize`) to guarantee a uniform distribution.
- `generateCode(blockLength, blockCount, uppercaseFirstBlock, allowedCharacters)` — builds the hyphen-separated secret and optionally uppercases the first block.
- `calculateEntropy(secretLength, charsetSize)` — returns `secretLength * log2(charsetSize)`.
- `getNumberInputValue(input, fallback)` — reads a numeric input, falling back when invalid and clamping to the input's `min`/`max`.
- `setSecretVisibility` / `setCopyButtonState` / `handleCopyClick` — UI helpers for masking, copy feedback, and clipboard writes via `navigator.clipboard`.

A single `keydown` listener on `document` implements the shortcuts, and an initial `generateAndDisplaySecret()` call produces a secret on page load.
