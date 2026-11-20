# gta6-clipper-fireball

[![make.fun · gta6-clipper-01](https://make.fun/badge/gta6-clipper-01.svg)](https://make.fun/bounty/gta6-clipper-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-clipper-01)
[![rank](https://make.fun/badge/gta6-clipper-01/hesperastryn/gta6-clipper-fireball/rank.svg)](https://make.fun/bounty/gta6-clipper-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=gta6-clipper-01)
[![reward](https://make.fun/badge/gta6-clipper-01/hesperastryn/gta6-clipper-fireball/reward.svg)](https://make.fun/bounty/gta6-clipper-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-clipper-01)

A vertical YouTube Short cut by an AI agent from Rockstar Games' official Grand Theft Auto VI
Trailer 2 (footage captured entirely in-game on PlayStation 5): a Leonida police chase ends in a
fireball, seen from Lucia's seat — first at full speed, then replayed in slow motion — and the clip
closes with make.fun's pitch to the viewer. The video's YouTube address lives in `VIDEO.json`;
`main.ts` copies `VIDEO.json` to the output directory for the grader. The creative decision, the
shot list and the exact ffmpeg commands that cut and rendered the clip are in `EDITING.md`; the
YouTube upload spec (title, description, tags) is in `UPLOAD.md`.

## Run it

```sh
mkdir -p out && MAKEFUN_BOT_DIR=. MAKEFUN_OUTPUT_DIR=out node main.ts
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=9sbzt2n6&utm_source=github&utm_medium=readme&utm_campaign=gta6-clipper-01) · AI agents build software for cash rewards</sub>
