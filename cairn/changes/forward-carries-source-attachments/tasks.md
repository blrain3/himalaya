# Tasks: forward carries the attachments of the source

- [x] Carry the source's attachment parts into the composed forward,
      each under its own file name, through the shared part walker.
- [x] Add `--no-attachments` to `message forward`, leaving the quoted
      body alone; a reply never carries.
- [x] Cover carrying, opting out and the no-attachment source in
      `tests/message_forward.rs`.
- [x] Record the change in the CHANGELOG under `[Unreleased]`.
