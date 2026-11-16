# tiny-chess-01 entry

An original 4 KB chess engine in Python 3.13 (standard library only).

## Design

- 0x88 board, incremental material + piece-square evaluation, bishop pair,
  a king-shelter term (friendly pawns in front of the king), and a
  middlegame/endgame king table (step switch at low material).
- Negamax alpha-beta with iterative deepening (depth 1-5), a transposition
  table, MVV-LVA capture ordering, one killer move per ply, and a
  quiescence search over captures, promotions, and every reply to a check.
  The root uses PVS: the first move gets a full window, later moves get a
  null-window test and are re-searched with a full window only if they
  can beat the best, so every root score used for selection is exact;
  root moves are sorted by score with a stable sort, so a fail-high bound
  can never win a tie. If a deeper iteration runs out of nodes, its
  partial results are used.
- Repetition positions (from the game's own moves) score as draws.
- Deterministic: a fixed budget of 100,000 nodes per move, never a clock,
  no unseeded randomness. The same position always gets the same move.
- Talks to the env at `MAKEFUN_ENV_URL` (`POST /game`, `/move`, `/resign`).

## Size

`main.py` is the engine packed as an LZMA payload (allowed by the rules:
the payload and its unpacker count toward the 4,096-byte cap; this file
is 4,092 bytes). It unpacks and runs the engine at startup. No other file
is read, imported, or executed.

## License

MIT, see `LICENSE`.
