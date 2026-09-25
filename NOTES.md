# Fundraiser × Codama — notes

## Versions
- anchor-cli: 1.1.2
- node: v22.14.0
- yarn: 1.22.22
- solana-cli: 4.1.2 (localnet tests only; no mainnet/devnet spend)
- @codama/cli: 1.6.3
- @codama/renderers-js: 2.5.0
- @solana/kit: 8.3.0
- @coral-xyz/anchor: 0.32.1
- surfpool: 1.6.0

## TODO 3 — why fundraiser and vault stay required

Codama only auto-fills accounts whose seeds it can prove from values already in the instruction. `contributorAccount` is seeded by `["contributor", fundraiser, contributor]` and `contributorAta` is an ATA of `(contributor, mintToRaise)`, so both can be derived once those other accounts are known. `tokenProgram` and `systemProgram` are constant addresses.

`fundraiser` is seeded by `["fundraiser", fundraiser.maker]`. That seed is a field *inside* the fundraiser account itself, not another account in the instruction, so there is a cycle: you would need the account to derive the account. `vault` is the ATA of `(fundraiser, fundraiser.mint_to_raise)`, which again needs data stored on `fundraiser`. In `initialize`, the same PDA can be derived from the `maker` signer you already pass; in `contribute`, the maker is not an instruction account, only `fundraiser.maker` in account data. That is why those two stay required.

## Bonus
Not attempted.

## One surprising thing
The generated Kit client and the Anchor client produced identical `contribute` instruction bytes and the same account order, even though one is generated at build time from the IDL and the other is built at runtime from the same IDL.
