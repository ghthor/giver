# The Giver

> It's the choosing that's important, isn't it?
>
> — <cite>Lois Lowry, The Giver</cite>

Giver gives IDs to stuff and lets you pick stuff by IDs. You can choose what
"stuff" is with a regex, but, by default, it's file paths.


## Usage

```bash
$ ls
# foo bar baz quz zurg beep.txt boop.md

$ ls | giver give | tee out.txt
# 1 foo
# 2 bar
# 3 baz
# 4 qux
# 5 zurg
# 6 beep.txt
# 8 boop.md

$ cat out.txt | giver pick 1 3 5-8
# foo
# baz
# zurg
# beep.txt
# boop.md
```


### Cooler Usage

```bash
$ ls | giver give --out=json | tee out.json
# {1: "foo", 2: "bar", 3: "baz", ...}

$ cat out.json | giver pick --in=json 1
# foo

$ cat out.json | giver pick --in=json --out=json 1
# {1: "foo"}

$ cat out.json | giver pick --in=json --out=idlist 1
# 1 foo

$ path_regex='[^\s]+$' # whatever
$ ls -l | giver give --regex=$path_regex
# 1 foo
# 2 bar
# 3 baz
# 4 qux
# 5 qux
# 6 beep.txt
# 7 boop.md

$ ls -l | giver give --regex=$path_regex --out=sub
# drw-r--r-- 1 jonas wheel 399 Jul 15 10:36 1 foo
# drw-r--r-- 1 jonas wheel 399 Jul 15 10:36 2 bar
# drw-r--r-- 1 jonas wheel 399 Jul 15 10:36 3 baz
# drw-r--r-- 1 jonas wheel 399 Jul 15 10:36 4 qux
# drw-r--r-- 1 jonas wheel 399 Jul 15 10:36 5 qux
# -rw-r--r-- 1 jonas wheel 399 Jul 15 10:36 6 beep.txt
# -rw-r--r-- 1 jonas wheel 399 Jul 15 10:36 7 boop.md

$ ls | giver give --format '[{}]'
# [1] foo
# [2] bar
# [3] baz
# [4] qux
# [5] zurg
# [6] beep.txt
# [8] boop.md
# NOTE: --out=json --format not allowed! (but OK with --out=sub)
# NOTE: --format is just for presenting output. If you wanna give it to
# `giver pick` just re-run `giver give`

# FIXME: `giver` is a shit name. But I'm kind of growing fond of its dorkiness.
```

## Summary

- `giver`
  - `give`
    - `--regex=<regex of things to match>`. Default: file paths
    - `--out=[json|sub|idlist]`. Default: `idlist` (`<ID> <thing>`)
    - `--format=<string with {} as placeholder for ID`
      - Not allowed with `--out=json`
  - `pick`
    - `[IDs]`. Single IDs or rangers (e.g. `2-4`), separated by space
    - `--in=[json|idlist]`. Default: `idlist`
    - `--out=[json|bare|idlist]`. Default `bare`
    - `--format=<string with {} as placeholder for <ID>`
      - Not allowed with `--out=json` or `--out=bare`


## Design goals

Giver's aim is to provide the same functionality that tools like
[`scm_breeze`](https://github.com/ndbroadbent/scm_breeze) and
[`fpp`](https://facebook.github.io/PathPicker/) provide, but in a more
focused, more Unix-y way.

That means:
- doesn't have a UI (omg `fpp`), it takes shit from `STDIN` and it puts shit on
  `STDOUT`
- it's not complected with other stuff (like git management)
- it's not written in freaking bash (probably go or haskell)
