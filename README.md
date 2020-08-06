# conventions
conventions i use to be consistent as much as possible.

# git
## commits
* use the `english language`
* use `imperative` and `present tense`
* use `commas` as delimiter
* keep everything `lowercase`
* keep descriptions as `short` as possible
* commit each file `individually`
* no bullshit commits, like: `i have done something`, `find the mistake`
    * no stupid mass commits, like: `git commit *`

example:
```bash
$ git commit README.md --message="write more about committing"
```

## branch naming convention
* use `underlines` as description delimiter

```no-highlight
<issue_number>/feature/<short_description>
<issue_number>/bugfix/<short_description>
<issue_number>/hotfix/<short_description>
<issue_number>/refactor/<short_description>
<issue_number>/experimental/<short_description>
<issue_number>/release/<short_description>
<issue_number>/merge/<short_description>
```

example:
```no-highlight
3/feature/client_zsh_config
```
```bash
$ tree .git/{logs,refs}
```
```no-highlight
logs
├── HEAD
└── refs/
    ├── heads/
    │   ├── 3/
    │   │   └── feature/
    │   │       └── client_zsh_config
    │   └── master
    └── remotes/
        └── origin/
            ├── 3/
            │   └── feature/
            │       └── client_zsh_config
            └── master
refs
├── heads/
│   ├── 3/
│   │   └── feature/
│   │       └── client_zsh_config
│   └── master
├── remotes/
│   └── origin/
│       ├── 3/
│       │   └── feature/
│       │       └── client_zsh_config
│       └── master
└── tags/
    ├── v1.0.0
    └── v1.1.0
```

# editing configuration files
* always keep the default by `commenting it`
* every edit causes a further comment underneath, to make backtraces possible

```no-highlight
<comment_sign> custom - YYYYMMDD - <issue_number> - <first_letter_of_firstname><lastname>: <short_description>
```

example:
```no-highlight
#COMMON_FLAGS="-O2 -pipe"
# custom - 20200805 - 2 - rfischer: set "-march" and "-mtune" to "ivybridge", set "-ftree-vectorize" and use "-O2"
COMMON_FLAGS="-ftree-vectorize -O2 -pipe -march=ivybridge -mtune=ivybridge"
```

# ansible
## variables
## task structure
## and so on

# issues
## structure
short description

working steps

check list

---
sources
