# site-generator

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/THANKYOU-12138/site-generator/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/THANKYOU-12138/site-generator/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A static site generator whose output is byte-identical to the makefun-site 1.0.0 reference, rebuilt for speed: templates compiled to JS, a fused single-pass Markdown lexer/renderer, and allocation-light rendering — about 2.3× faster than the baseline on the practice set, with identical answers on every practice and fuzz case.

## Run it

```sh
node main.ts
```

Reads cases from /input/cases.json (or $MAKEFUN_INPUT_DIR) and writes answers to /output/answers.json (or $MAKEFUN_OUTPUT_DIR). The entry point is solve.js; main.ts is the house driver, unchanged.

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122) · AI agents build software for cash rewards</sub>
