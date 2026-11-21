# tiny-chess-4k

![bounty](https://make.fun/badge/tiny-chess-01.svg)
![rank](https://make.fun/badge/tiny-chess-01/elangramsyah/tiny-chess-4k/rank.svg)
![reward](https://make.fun/badge/tiny-chess-01/elangramsyah/tiny-chess-4k/reward.svg)

A chess engine in a single 4,086-byte Python file, written for the
[tiny-chess-01](https://make.fun/bounty/tiny-chess-01) bounty on make.fun:
the strongest engine that fits in 4 KB.

## How it plays

- Mailbox board with fully legal move generation (castling, en passant,
  promotions), make/unmake, and incremental Zobrist keys.
- Iterative-deepening negamax with alpha-beta pruning and a fixed budget
  of 40,000 nodes per move — the search never reads a clock, so replays
  are deterministic.
- Transposition table with mate-distance score adjustment; move ordering
  by TT move, MVV-LVA captures, promotions, killer moves, then a flat
  history table indexed by side, origin and target square.
- Check extensions, plus a quiescence search over captures, promotions
  and check evasions. Repetition (game history and the current line) and
  the 100-ply rule score as draws inside the search.
- Tapered evaluation: material (P100 N320 B330 R500 Q900) blended with
  hand-written midgame/endgame piece-square tables for every piece, plus
  a bishop-pair bonus that grows toward the endgame.

## Measured strength

On the kit's local practice trials — six games each against the house
gauntlet (alpha-beta at depths 2/3/4 and node caps of 100k/250k/500k,
rated 855 to 1596) — the shipped packed file scores:

- trial-0: 4.0/6 (W W W L W L), Elo 1451
- trial-1: 3.5/6 (W L W D D L), Elo 1279

Move generation is perft-exact on the standard test positions, and both
trial runs above were played end-to-end by this `main.py` itself.
- Safety net: the chosen move is always checked against the env's
  `legal_moves`; anything unexpected falls back to the best 1-ply move.

## Running it

Standard library only — no model calls, no network except the game env.

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:PORT python3 main.py
```

The driver loops `POST /game` and `POST /move` until the env reports the
trial done.

## License

MIT — see `LICENSE`.
