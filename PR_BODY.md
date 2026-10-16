Closes #780.

## What this does now

Narrowed to the shape agreed in #780, as requested in review:

- **The quoted body composes exactly as it did.** No MIME-tree rebuild: the body, the headline, the signature and the posting style ride the same `compose_body` path as before. Forwarding a source richer than a single text body no longer restructures anything.
- **The source's attachments ride along**, each as an attachment part of its own, under the file name the source gave it, with its content type and decoded bytes. Parts are read through the shared `message::part` walker (role, file name, MIME, bytes) — the same one `message read`, `attachment list` and `attachment download` use.
- **`--no-attachments` leaves them out**, keeping the quoted body alone. It combines with `--attach` for a writer replacing the source's attachments with files of their own. A reply carries nothing.

A source with no attachment part composes exactly as before (the no-attachment test is the control).

## Evidence

Tests: `tests/message_forward.rs` (new), `cargo test --test message_forward --all-features`:

- Before the fix (source at `master`, tests kept): **2 failed, 1 passed**
  - `forward_carries_the_attachment_of_the_source` — FAILED
  - `forward_keeps_the_quoted_body_alone_without_attachments` — FAILED
  - `forward_of_a_source_without_attachment_keeps_the_text_form` — ok (control)
- After the fix: **3 passed, 0 failed**

Gates run locally: `cargo fmt --check`, `cargo clippy --all-features --all-targets` (clean), `cargo test --lib`, `cargo test --test message_forward --test message_reply --test message_parse --all-features` (all green). The reusable `pimalaya/nix` CI runs on push.

## Spec and changelog

- Cairn change `forward-carries-source-attachments`: `delta.md` (two ADDED requirements), `proposal.md`, `tasks.md`.
- `CHANGELOG.md`: one entry under `[Unreleased] / Added`.

## Design choices (happy to switch)

- A part the source left nameless is **skipped rather than renamed** — an anonymous attachment is of no use to the recipient. Alternative: name it `attachment`. Say the word.
- `--no-attachments` does **not** conflict with `--attach`: dropping the source's files and adding your own are independent acts.
- Reply passes `no_attachments` outright (a reply never carried anything).

## AI disclosure

Implemented by an AI agent (Hermes), reviewed by a second AI agent (OpenCode) and by the human maintainer of this fork. Every line is attributable; no upstream code was copied beyond the shared `message::part` helpers this change calls.
