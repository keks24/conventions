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
.git/logs
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
.git/refs
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

# issue tracking
## structure
### jira
```no-highlight
subject: <main_subject>: <short_description>

<detailed_description>

# working steps
<chronological_list>

# check list
<fuzzy_list>
---
<link_to_source1>
<link_to_source2>
<link_to_sourcen>
```

example:
```no-highlight
New backup solution: Set up a decent backup server

Since there is no decent backup solution yet, one has to be found. Maybe the following:

"Bareos"
"Borg"
"dd"
"rsync"

# Working steps
(x) Requirement analysis
    (x) Which program would be suitable for our infrastructure?
    (x) Which RAID method should be used?
    (x) Which hard drives are the best?
        (x) Research: mSATA, M2, SSD, HDD (10.000 RPM)
(x) Do a research about decent backup solutions and test all of them
    (x) Bareos
    (x) Borg
    (x) dd
    (x) rsync
(x) Do a test for several weeks/months

# Check list
(x) backup_server1 (Bareos)
(x) backup_server2 (Borg)
(x) backup_server3 (dd)
(x) backup_server4 (rsync)
---
https://<i_found_some_information_here>.com
https://<i_found_some_information_there>.com
```

### git issues
issue templates
Ansible
Gitea
