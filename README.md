# conventions
conventions i use to be consistent as much as possible.

Table of Contents
=================
* [git](#git)
   * [commits](#commits)
      * [example commit](#example-commit)
   * [branch naming convention](#branch-naming-convention)
      * [example branch name](#example-branch-name)
* [placeholder variables](#placeholder-variables)
   * [example variables](#example-variables)
      * [Bash](#bash)
      * [Anything else](#anything-else)
* [editing configuration files](#editing-configuration-files)
   * [example configuration edit](#example-configuration-edit)
* [ansible](#ansible)
   * [general](#general)
   * [ansible.cfg](#ansiblecfg)
      * [example configuration file](#example-configuration-file)
   * [modules](#modules)
      * [general](#general-1)
      * [copy](#copy)
      * [synchronize](#synchronize)
      * [template](#template)
      * [git](#git-1)
      * [shell](#shell)
      * [replace](#replace)
      * [lineinfile](#lineinfile)
   * [variables](#variables)
   * [role structure](#role-structure)
      * [example handlers](#example-handlers)
         * [roles/aria2c/handlers/aria2c_systemd_handlers.yml](#rolesaria2chandlersaria2c_systemd_handlersyml)
   * [task structure](#task-structure)
      * [example tasks](#example-tasks)
         * [common_packages.yml](#common_packagesyml)
         * [harden_ssh.yml](#harden_sshyml)
         * [prepare_directories.yml](#prepare_directoriesyml)
         * [gitalias.yml](#gitaliasyml)
         * [main.yml](#mainyml)
         * [tmux_global.yml](#tmux_globalyml)
* [issue tracking](#issue-tracking)
   * [structure](#structure)
      * [jira](#jira)
         * [example ticket](#example-ticket)
      * [git issues](#git-issues)
         * [to-do](#to-do)

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

### example commit
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

### example branch name
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

# placeholder variables
* use the personalised variable `nom` instead of `test`.
    * `test` is built-in command in `Bash`
* `<some_short_description>`
    * `some_` is a prefix to emphasise, that it is a `placeholder variable`

## example variables
### Bash
```bash
$ nom="123"
$ echo "${nom}"
```

### Anything else
```no-highlight
/home/<some_username>/downloads/
```

# editing configuration files
* always keep the default by `commenting it`
* every edit causes a further comment underneath, to make backtraces possible

```no-highlight
<comment_sign> custom - YYYYMMDD - <issue_number> - <first_letter_of_firstname><lastname>: <short_description>
```

## example configuration edit
```no-highlight
#COMMON_FLAGS="-O2 -pipe"
# custom - 20200805 - 2 - rfischer: set "-march" and "-mtune" to "ivybridge", set "-ftree-vectorize" and use "-O2"
COMMON_FLAGS="-ftree-vectorize -O2 -pipe -march=ivybridge -mtune=ivybridge"
```
```no-highlight
# custom - 20200806 - rfischer: use "elogv" to read log files from "portage"
PORTAGE_ELOG_CLASSES="warn error log"
PORTAGE_ELOG_SYSTEM="save"
```

# ansible
## general
* be idempotent: try to get `ok` as much as possible, when executing a `playbook` or `role` twice.
* avoid using the `shell` module as much as possible to gurantee idempotency
* write comments in `lowercase`

## `ansible.cfg`
* use `ssh pipelining` and not the `deprecated accelerate` option!!

### example configuration file
```ini
[defaults]
host_key_checking       = no
force_handlers          = no
private_role_vars       = yes
forks                   = 2
gathering               = smart
fact_caching            = jsonfile
fact_caching_connection = /home/ansible/.local/etc/ansible/cached_facts/
fact_caching_timeout    = 86400
# bookmark "fact_path" does not work somehow; create an issue on github?
fact_path               = /home/ansible/.ansible/facts.d/
log_path                = /var/log/ansible/ansible.log
#vault_password_file     = ~/.ansible/vault_pass
remote_tmp              = /home/ansible/.ansible/tmp/
local_tmp               = /home/ansible/.ansible/tmp/
#retry_files_enabled    = False
retry_files_save_path   = /home/ansible/.local/etc/ansible/retry/
roles_path              = /home/ansible/git/provisioning/roles/
ansible_managed         = this file is managed by ansible, all changes will be lost!
stdout_callback         = yaml
nocows                  = 1
interpreter_python      = auto

[ssh_connection]
ssh_args                = -o ControlMaster=auto -o ControlPersist=10m -o UserKnownHostsFile=/dev/null -o IdentitiesOnly=yes
# "control_path_dir" is "%(directory)s" in "control_path"
control_path_dir        = /home/ansible/.ssh/cm/
control_path            = %(directory)s/ansible-%%r@%%h:%%p[%%n].cm
transfer_method         = scp
# when using sudo (become: yes): disable "requiretty" in "/etc/sudoers" on all managed hosts for "pipelining" (should be disabled by default)
pipelining              = true

[inventory]
enable_plugins          = yaml, host_list
ignore_extensions       = .bak, .cfg, .ini, .md, .orig, .pyc, .pyo, .retry, .rpm, .swp, .txt, ~
```

## modules
### general
* use absolute paths everywhere
* add a `trailing slash`, if a path ends with a `directory name` or `mentioning a directory`
    * this does not apply for `symlink destinations`, since this `results into an error`
* `reload daemons` whenever possible, `restart` them, if there is no other option
* write custom scripts in `Bash`
    * log executions in `/var/log/syslog` using `logger`: `/usr/bin/logger --tag "remove cache files" --id --stderr "${script_directory_path}/${script_name}: executed"`
* when installing a `new package`, immediately call `enable` and `restart` handler, if there is a way to control the daemon
* use `validate` as much as possible to validate, that the manipulated file has `correct syntax!`: Very important for changes in `/etc/ssh/sshd_config` and so forth!

### copy
* use this module to copy a `single file`
* using this module to copy `multiple files` may `result in an error!`

### synchronize
* use this module to copy `multiple files`

### template
* when `copying template files`, always use `# {{ ansible_managed }}` at the `top of the file` to indicate that the file was `copied using Ansible` and `should not be edited` without thought

### git
* when cloning, always use `force: "yes"`

### shell
* to `sanitise` any variables, use `{{ var | quote }}`, instead of `{{ var }}`, to make sure, they do not include `evil things like semicolons`
* make it idempotent by creating files in the directory `/usr/local/etc/.ansible_dotfile_switches` and using `creates`, if it does make sense
    * it might be better to create `custom Ansible facts` and using `when` for this
* when using the module, use `creates` to gurantee idempotency

### replace
* when `uncommenting lines`, use this module, instead of `lineinfile` to avoid redundant options
* always use `very precise regular expressions` to find a certain keyword: `/etc/ssh/sshd_config/`: `(.)?.*Port.*[0-9]{1,5}`
* when writing regular expressions, use `double quotes`, if necessary, use `single quotes`

### lineinfile
* always use `very precise regular expressions` to find a certain keyword: `/etc/ssh/sshd_config/`: `(.)?.*Port.*[0-9]{1,5}`
* when writing regular expressions, use `double quotes`, if necessary, use `single quotes`

## variables
* write `custom variables in uppercase` to have a `clear separation` between `custom variables`, `Ansible variables and facts`
* when using variables in `ansible vault`, use `VAULT_` as prefix

## role structure
* use `underscore` as delimiter for role `directory names`
* `mirror the remote directory structure` in roles to know where the file will be placed, so analysing the `playbook` is not necessary: `files/etc/zsh/zshrc.local`, `templates/etc/nginx/sites-available/01-domain-name.local`
* use `meta` directory to include dependencies
* daemons should be controlled by `handlers`
    * every possible way to control the daemon should be available in the directory `handlers`

### example handlers
#### `roles/aria2c/handlers/aria2c_systemd_handlers.yml`
```yml
---
- name: handler_start_aria2c
  systemd:
    name: "aria2c.service"
    daemon_reload: "yes"
    state: "started"

- name: handler_stop_aria2c
  systemd:
    name: "aria2c.service"
    daemon_reload: "yes"
    state: "stopped"

- name: handler_restart_aria2c
  systemd:
    name: "aria2c.service"
    daemon_reload: "yes"
    state: "restarted"

- name: handler_enable_aria2c
  systemd:
    name: "aria2c.service"
    daemon_reload: "yes"
    enabled: "yes"

- name: handler_disable_aria2c
  systemd:
    name: "aria2c.service"
    daemon_reload: "yes"
    enabled: "no"
```

## task structure
* use the new `newline syntax` and do use the `equal sign syntax` to improve readability!
* always use the `main.yml` to `include` other `.yml` files
    * also `set variables` for met conditions
* write task names in `lowercase`
    * write them `without quotes` and `emphasise` keywords with quotes
    * the description limit should be `80 characters`
    * describe as distinct as possible
* always use `quotes`, when writing the `value part`: `path: "/etc/ssh/sshd_config"`
* `add tags` at the end of every task: `the role name` is the first tag (more follows, if it makes sense)
* use `include_*` modules, when using conditionals (`when`) and `import_*` for anything else
* always use `yaml lists`, when writing `tags` or `when` statements
* if `compiling` is necessary, do every step as `separate task`. this makes `debugging` easier
* always execute `*_global.yml` playbooks at the `beginning` within a `main.yml` file
* always use `with_items`, when `creating subdirectories` with the `file` module to improve readability
* always use `newlines`, when using `objects` within `with_items` to improve readability
* when having `multiple objects` in `with_items`, use `<MODULE_NAME>_<MODULE_OPTION>:` as variables for each option: `FILE_PATH:`, `FILE_MODE:`
* always write `states` at the `bottom` of a task
* `override` variables in `defaults/main.yml` with an `empty string`, when invoking the role with a parameter: `- { role: "somerole", VARIABLE: "something" }`
* when `installing multiple packages`, do not use `with_items`, use a `yaml list` instead, otherwise every package gets installed `one by one`, which is very slow
* try to `avoid changing` default configuration files as much as possible
    * look for a way to set `custom settings`
    * look for `directories`, which were made for this: `/etc/logrotate.d/`, `/etc/cron.d/`
* when installing packages, use the following convention: `<write_what_it_does> (<package_name>)`
    * if there are `multiple packages`, do not include any package name

### example tasks
#### `common_packages.yml`
```yml
- name: install common tools
  package:
    name:
      - "curl"
      - "file"
      - "gawk"
  [...]
```

#### `harden_ssh.yml`
```yml
- name: set ssh port (live)
  replace:
    path: "/etc/ssh/sshd_config"
    regexp: "(.)?.*Port.*[0-9]{1,5}"
    replace: "Port {{ SSH_PORT_NUMBER }}"
    validate: "/usr/sbin/sshd -t -f %s"
  when:
    - SSHD_SETTINGS_SET.stat.exists == false
    - LOCAL_TEST == "no"
  notify:
    - handler_reload_sshd
  tags:
    - harden_sshd
```

#### `prepare_directories.yml`
```yml
- name: create directory for man pages
  file:
    path: "/usr/local/share/{{ item }}/"
    owner: "root"
    group: "staff"
    mode: "0755"
    state: "directory"
  with_items:
    - "man"
    - "man/man1"
    - "man/man2"
    - "man/man3"
    - "man/man4"
    - "man/man5"
    - "man/man6"
    - "man/man7"
    - "man/man8"
    - "man/man9"
  tags:
    - prepare_directories
```

#### `gitalias.yml`
```yml
- name: create gitconfig skeleton directories
  file:
    path: "/etc/skel/{{ item.FILE_PATH }}"
    owner: "root"
    group: "root"
    mode: "{{ item.FILE_MODE }}"
    state: "{{ item.FILE_STATE }}"
  with_items:
    - { FILE_PATH: ".gitconfig",
        FILE_MODE: "0644",
        FILE_STATE: "touch" }
    - { FILE_PATH: ".gitconfig.d/",
        FILE_MODE: "0755",
        FILE_STATE: "directory" }
  tags:
    - gitalias
```

#### `main.yml`
```yml
- import_tasks: "neovim_global.yml"
  tags:
    - neovim

- include_tasks: "neovim_root.yml"
  when:
    - USERNAME == "root"
  tags:
    - neovim

- include_tasks: "neovim_other_user.yml"
  when:
    - USERNAME != "root"
  tags:
    - neovim
```

#### `tmux_global.yml`
```yml
- name: install terminal multiplexer (tmux)
  package:
    name: "tmux"
    cache_valid_time: "{{ CACHE_VALID_TIME }}"
    update_cache: "yes"
    state: "{{ PACKAGE_STATE }}"
  tags:
    - tmux
```

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

#### example ticket
```no-highlight
New backup solution: Set up a decent backup server

Since there is no decent backup solution yet, one has to be found. Maybe one the following:

* "Bareos"
* "Borg"
* "dd"
* "rsync"

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
#### to-do
* issue templates
* Ansible
* Gitea
