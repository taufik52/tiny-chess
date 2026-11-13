# Tiny Chess — a 4 KB chess engine

A chess engine in a single `main.py` of 4,069 bytes, written for the
[make.fun](https://make.fun/) `tiny-chess-01` bounty (strongest chess engine
in 4,096 bytes of Python). It plays the house gauntlet over HTTP: eight games
against a ladder of alpha-beta engines, from depth-2 up to a 2M-node search.

## How it plays

- **Board:** 0x88 representation, incremental evaluation (material +
  piece-square tables with a middlegame/endgame taper, bishop-pair bonus).
- **Search:** alpha-beta with iterative deepening to a fixed node budget
  (25,000 nodes per move by default, `NODE_BUDGET` env override), a
  transposition table, null-move pruning (R=2), quiescence search
  (captures + promotions, full evasion when in check), MVV-LVA move
  ordering, and repetition scoring from the game history.
- **Correctness:** perft-verified move generation (start position,
  Kiwipete, and three tricky positions, all depths match published counts),
  and incremental state (score, material, hash, king squares) checked
  against from-scratch recomputation over random games.
- **Deterministic:** fixed node budget, never a clock; fixed-seed Zobrist
  keys. Standard library only.

`main.py` is a self-contained stub: it decompresses an LZMA payload
(base85) and execs it. The payload and the unpacker together are the
4,069 bytes. No other file of this repository is read, imported, or
executed.

## Running it

The engine talks to the competition env over HTTP:

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:18080 NODE_BUDGET=25000 python3 main.py
```

It loops `POST /game` and `POST /move` until the env reports the trial
is done.

## License

MIT. See `LICENSE`.

## Submission notes

Built for the make.fun tiny-chess-01 bounty; entrypoint is the 4,069-byte packed `main.py`.
Trial-0: 3.5/8 vs the house ladder (Elo ~1,326), node budget 25,000 per move.
