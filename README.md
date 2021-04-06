# conventions
conventions i use to be consistent as much as possible.

Table of Contents
=================

* [git](#git)
   * [commits](#commits)
      * [example commit](#example-commit)
   * [branches](#branches)
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
         * [roles/nfs_share/handlers/nfs-kernel-server_handlers.yml](#rolesnfs_sharehandlersnfs-kernel-server_handlersyml)
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
      * [git](#git-2)
         * [example issue](#example-issue)

# git
## commits
* use the `english language`
* use `imperative` and `present tense`
* use `commas` as delimiter
* keep everything in `lowercase`
* keep descriptions as `short` as possible
* commit each file `individually`
    * do `in-line commits`, if possible
* no bullshit commits, like: `i have done something`, `find the mistake` or worse `git commit --message="$(curl 'http://api.icndb.com/jokes/random')"` **YES**, Mr. H., I am looking at you! >:(
    * no stupid mass commits, like: `git commit *` or `git commit --all`

### example commit
```bash
$ git commit README.md --message="write more about committing"
```

## branches
* the branch `master` should `be clean!`, since with each commit one confirms, that the code works!
* experiments and tests should be done on `separate branches!`

### branch naming convention
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

#### example branch name
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
    * when using `conditional statements`, `nom` means `OK` and `NOM` means `not OK`.
* `<some_short_description>`
    * `some_` is a prefix to emphasise, that it is a `placeholder variable`

## example variables
### Bash
```bash
$ nom="123"
$ echo "${nom}"
```
```bash
$ nom="123"
$ if [[ "${nom}" == "123" ]]; then echo "nom"; else echo "NOM"; fi
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
* when `copying template files`, always use `<comment_sign> {{ ansible_managed }}` at the `top of the file` to indicate that the file was `copied using Ansible` and `should not be edited` without thought

### git
* when cloning, always use `force: "yes"`

### shell
* to `sanitise` any variables, use `{{ var | quote }}`, instead of `{{ var }}`, to make sure, they do not include `evil things like semicolons`
* make it idempotent by creating files in the directory `/usr/local/etc/ansible/dotfile_switches` and using `creates`, if it does make sense
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
    * handler names always start with the prefix `handler_`
    * keep everything in `lowercase`

### example handlers
#### `roles/nfs_share/handlers/nfs-kernel-server_handlers.yml`
```yml
---
- name: handler_start_nfs-kernel-server
  systemd:
    name: "nfs-kernel-server.service"
    daemon_reload: "yes"
    state: "started"

- name: handler_stop_nfs-kernel-server
  systemd:
    name: "nfs-kernel-server.service"
    daemon_reload: "yes"
    state: "stopped"

- name: handler_restart_nfs-kernel-server
  systemd:
    name: "nfs-kernel-server.service"
    daemon_reload: "yes"
    state: "restarted"

- name: handler_reload_nfs-kernel-server
  systemd:
    name: "nfs-kernel-server.service"
    daemon_reload: "yes"
    state: "reloaded"

- name: handler_enable_nfs-kernel-server
  systemd:
    name: "nfs-kernel-server.service"
    daemon_reload: "yes"
    enabled: "yes"

- name: handler_disable_nfs-kernel-server
  systemd:
    name: "nfs-kernel-server.service"
    daemon_reload: "yes"
    enabled: "no"

- name: handler_reexport_nfs-kernel-server
  shell: "/usr/sbin/exportfs -r"
```

## task structure
* use the new `newline syntax` and avoid the `equal sign syntax` to improve readability!
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

### git
* Ansible
    * https://github.com/ansible/ansible/issues/new/choose
    * https://github.com/ansible/ansible/tree/devel/.github
* Gitea
    * https://github.com/go-gitea/gitea/tree/master/.github
* LineageOS
    * https://gitlab.com/LineageOS/issues/android/-/issues
* Gluster
    * https://github.com/gluster/glusterfs/tree/master/.github

#### example issue
* https://github.com/ansible/ansible/issues/66020

````no-highlight
<!--- Verify first that your issue is not already reported on GitHub -->
<!--- Also test if the latest release and devel branch are affected too -->
<!--- Complete *all* sections as described, this form is processed automatically -->

##### SUMMARY
<!--- Explain the problem briefly below -->
I am using prompt in vars_prompt to manipulate the variable serial in a playbook to define how many of my hosts should be updated at once. I am not sure, if this should be possible, because I am using a variable before it was actually defined. I always guessed, that a playbook is read top-down.
Is this intended or did I find a bug?

##### ISSUE TYPE
Bug Report

##### COMPONENT NAME
<!--- Write the short name of the module, plugin, task or feature below, use your best guess if unsure -->
lib/ansible/executor/playbook_executor.py#L115

##### ANSIBLE VERSION
<!--- Paste verbatim output from "ansible --version" between quotes -->
```bash
ansible 2.7.7
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/ansible/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.7.3 (default, Apr  3 2019, 05:39:12) [GCC 8.3.0]
The latest stable and devel version are also affected:
```
```bash
ansible 2.9.2.post0
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/home/ansible/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /tmp/ansible/lib/ansible
  executable location = /tmp/ansible/bin/ansible
  python version = 2.7.16 (default, Oct 10 2019, 22:02:15) [GCC 8.3.0]
```
```bash
ansible 2.10.0.dev0
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/home/ansible/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /tmp/ansible/lib/ansible
  executable location = /tmp/ansible/bin/ansible
  python version = 2.7.16 (default, Oct 10 2019, 22:02:15) [GCC 8.3.0]
```

##### CONFIGURATION
<!--- Paste verbatim output from "ansible-config dump --only-changed" between quotes -->
```bash
ANSIBLE_NOCOWS(/home/ansible/provisioning/ansible.cfg) = True
ANSIBLE_PIPELINING(/home/ansible/provisioning/ansible.cfg) = True
ANSIBLE_SSH_ARGS(/home/ansible/provisioning/ansible.cfg) = -o ControlMaster=auto -o ControlPersist=5m -o UserKnownHostsFile=/dev/null -o IdentitiesOnly=yes
ANSIBLE_SSH_CONTROL_PATH(/home/ansible/provisioning/ansible.cfg) = %(directory)s/ansible-%%r@%%h:%%p[%%n].cm
ANSIBLE_SSH_CONTROL_PATH_DIR(/home/ansible/provisioning/ansible.cfg) = /home/ansible/.ssh/cm/
CACHE_PLUGIN(/home/ansible/provisioning/ansible.cfg) = jsonfile
CACHE_PLUGIN_CONNECTION(/home/ansible/provisioning/ansible.cfg) = /home/ansible/.ansible/cached_facts/
CACHE_PLUGIN_TIMEOUT(/home/ansible/provisioning/ansible.cfg) = 86400
DEFAULT_FACT_PATH(/home/ansible/provisioning/ansible.cfg) = /home/ansible/.ansible/facts.d
DEFAULT_FORCE_HANDLERS(/home/ansible/provisioning/ansible.cfg) = False
DEFAULT_FORKS(/home/ansible/provisioning/ansible.cfg) = 2
DEFAULT_GATHERING(/home/ansible/provisioning/ansible.cfg) = smart
DEFAULT_LOCAL_TMP(/home/ansible/provisioning/ansible.cfg) = /home/ansible/.ansible/tmp/ansible-local-20185gdirvb_u
DEFAULT_LOG_PATH(/home/ansible/provisioning/ansible.cfg) = /var/log/ansible/ansible.log
DEFAULT_MANAGED_STR(/home/ansible/provisioning/ansible.cfg) = this file is managed by ansible, all changes will be lost!
DEFAULT_PRIVATE_ROLE_VARS(/home/ansible/provisioning/ansible.cfg) = True
DEFAULT_ROLES_PATH(/home/ansible/provisioning/ansible.cfg) = ['/home/ansible/provisioning/roles']
DEFAULT_SSH_TRANSFER_METHOD(/home/ansible/provisioning/ansible.cfg) = scp
DEFAULT_STDOUT_CALLBACK(/home/ansible/provisioning/ansible.cfg) = yaml
HOST_KEY_CHECKING(/home/ansible/provisioning/ansible.cfg) = False
INVENTORY_ENABLED(/home/ansible/provisioning/ansible.cfg) = ['yaml', 'host_list']
INVENTORY_IGNORE_EXTS(/home/ansible/provisioning/ansible.cfg) = ['.bak', '.cfg', '.ini', '.md', '.orig', '.pyc', '.pyo', '.retry', '.rpm', '.swp', '.txt', '~']
RETRY_FILES_SAVE_PATH(/home/ansible/provisioning/ansible.cfg) = /home/ansible/.ansible/retry
```

##### OS / ENVIRONMENT
<!--- Provide all relevant information below, e.g. target OS versions, network device firmware, etc. -->
###### Ansible host
```bash
$ uname -a
Linux ansible 5.3.13-1-pve #1 SMP PVE 5.3.13-1 (Thu, 05 Dec 2019 07:18:14 +0100) x86_64 GNU/Linux
```
```bash
$ < /etc/os-release
PRETTY_NAME="Debian GNU/Linux 10 (buster)"
NAME="Debian GNU/Linux"
VERSION_ID="10"
VERSION="10 (buster)"
VERSION_CODENAME=buster
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```

##### STEPS TO REPRODUCE
<!--- Describe exactly how to reproduce the problem, using a minimal test-case -->
<!--- Paste example playbooks or commands between quotes below -->
<!--- HINT: You can paste gist.github.com links for larger files -->
1. Create the following playbook in `/home/ansible/provisioning/playbooks/`:

`update_packages.yml`

```yml
---
- hosts: "all"
  serial: "{{ PROMPT_CHECK_HOST_AMOUNT }}"
  any_errors_fatal: "yes"
  vars_prompt:
    - name: PROMPT_CHECK_HOST_AMOUNT
      prompt: "How many hosts should be checked at once?"
      default: "2"
      private: "no"

  tasks:
    - name: update the package cache
      package:
        cache_valid_time: "86400"
        update_cache: "yes"
      tags:
        - update_packages

    - name: register upgradable packages
      shell: "apt list --upgradable"
      register: "UPGRADEABLE_PACKAGES_LIST"
      changed_when: "false"
      tags:
        - update_packages

    - name: save variable as ansible fact
      set_fact:
        UPGRADEABLE_PACKAGES_LIST: "{{ UPGRADEABLE_PACKAGES_LIST.stdout }}"
      tags:
        - update_packages

    - name: list upgradeable packages
      debug:
        var: UPGRADEABLE_PACKAGES_LIST
      tags:
        - update_packages

    - name: waiting for input
      pause:
        prompt: "check, if there are any critical updates. press 'enter' to continue, 'ctrl+c' to abort"
      tags:
        - update_packages

    - name: update all upgradable packages
      package:
        cache_valid_time: "86400"
        update_cache: "yes"
        upgrade: "yes"
        force_apt_get: "yes"
        state: "latest"
      tags:
        - update_packages

    - name: remove packages from cache
      package:
        autoclean: "yes"
      tags:
        - update_packages

    - name: remove dependencies
      package:
        autoremove: "yes"
      tags:
        - update_packages

  post_tasks:
    - name: update file database
      shell: "updatedb"
      changed_when: "false"
      tags:
        - update_packages
```

2. Execute the playbook:
```bash
$ ansible-playbook --inventory="inventory.yml" playbooks/update_packages.yml
```

3. Enter a number

##### EXPECTED RESULTS
<!--- Describe what you expected to happen when running the steps above -->
It should not work, since I am using a variable, which was actually not defined before.

##### ACTUAL RESULTS
<!--- Describe what actually happened. If possible run with extra verbosity (-vvvv) -->
<!--- Paste verbatim command output between quotes -->
```bash
ansible-playbook 2.7.7
  config file = /home/ansible/provisioning/ansible.cfg
  configured module search path = ['/home/ansible/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible-playbook
  python version = 3.7.3 (default, Apr  3 2019, 05:39:12) [GCC 8.3.0]
Using /home/ansible/provisioning/ansible.cfg as config file
setting up inventory plugins
Parsed /home/ansible/provisioning/inventory.yml inventory source with yaml plugin
Loading callback plugin yaml of type stdout, v2.0 from /usr/lib/python3/dist-packages/ansible/plugins/callback/yaml.py

PLAYBOOK: update_packages.yml *****************************************************************************************************************************************************************************************************
1 plays in playbooks/update_packages.yml
How many hosts should be checked at once? [2]: 1

PLAY [all] ************************************************************************************************************************************************************************************************************************
META: ran handlers

TASK [update the package cache] ***************************************************************************************************************************************************************************************************
task path: /home/ansible/provisioning/playbooks/update_packages.yml:28
Running apt
Using module file /usr/lib/python3/dist-packages/ansible/modules/packaging/os/apt.py
<192.168.0.101> ESTABLISH SSH CONNECTION FOR USER: ansible
<192.168.0.101> SSH: EXEC ssh -vvv -o ControlMaster=auto -o ControlPersist=5m -o UserKnownHostsFile=/dev/null -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o Port=1337 -o 'IdentityFile="/home/ansible/.ssh/id_ed25519"' -o KbdInteractiveAuthentication=no -o PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey -o PasswordAuthentication=no -o User=ansible -o ConnectTimeout=10 -o 'ControlPath=/home/ansible/.ssh/cm/ansible-%r@%h:%p[%n].cm' 192.168.0.101 '/bin/sh -c '"'"'sudo -H -S -n -u root /bin/sh -c '"'"'"'"'"'"'"'"'echo BECOME-SUCCESS-kysfxswcergbvcpevaqtibioblzdgamk; /usr/bin/python'"'"'"'"'"'"'"'"' && sleep 0'"'"''
Escalation succeeded
<192.168.0.101> (0, b'\n{"invocation": {"module_args": {"dpkg_options": "force-confdef,force-confold", "autoremove": false, "force": false, "force_apt_get": false, "install_recommends": null, "package": null, "autoclean": false, "purge": false, "allow_unauthenticated": false, "state": "present", "upgrade": null, "update_cache": true, "default_release": null, "only_upgrade": false, "deb": null, "cache_valid_time": 86400}}, "cache_updated": false, "changed": false, "cache_update_time": 1576932841}\n', b'OpenSSH_7.9p1 Debian-10+deb10u1, OpenSSL 1.1.1d  10 Sep 2019\r\ndebug1: Reading configuration data /etc/ssh/ssh_config\r\ndebug1: /etc/ssh/ssh_config line 19: Applying options for *\r\ndebug2: resolve_canonicalize: hostname 192.168.0.101 is address\r\ndebug1: auto-mux: Trying existing master\r\ndebug2: fd 3 setting O_NONBLOCK\r\ndebug2: mux_client_hello_exchange: master version 4\r\ndebug3: mux_client_forwards: request forwardings: 0 local, 0 remote\r\ndebug3: mux_client_request_session: entering\r\ndebug3: mux_client_request_alive: entering\r\ndebug3: mux_client_request_alive: done pid = 6894\r\ndebug3: mux_client_request_session: session request sent\r\ndebug3: mux_client_read_packet: read header failed: Broken pipe\r\ndebug2: Received exit status from master 0\r\n')
ok: [pihole.local] => changed=false 
  cache_update_time: 1576932841
  cache_updated: false
  invocation:
    module_args:
      allow_unauthenticated: false
      autoclean: false
      autoremove: false
      cache_valid_time: 86400
      deb: null
      default_release: null
      dpkg_options: force-confdef,force-confold
      force: false
      force_apt_get: false
      install_recommends: null
      only_upgrade: false
      package: null
      purge: false
      state: present
      update_cache: true
      upgrade: null

TASK [register upgradable packages] ***********************************************************************************************************************************************************************************************
task path: /home/ansible/provisioning/playbooks/update_packages.yml:35
Using module file /usr/lib/python3/dist-packages/ansible/modules/commands/command.py
<192.168.0.101> ESTABLISH SSH CONNECTION FOR USER: ansible
<192.168.0.101> SSH: EXEC ssh -vvv -o ControlMaster=auto -o ControlPersist=5m -o UserKnownHostsFile=/dev/null -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o Port=1337 -o 'IdentityFile="/home/ansible/.ssh/id_ed25519"' -o KbdInteractiveAuthentication=no -o PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey -o PasswordAuthentication=no -o User=ansible -o ConnectTimeout=10 -o 'ControlPath=/home/ansible/.ssh/cm/ansible-%r@%h:%p[%n].cm' 192.168.0.101 '/bin/sh -c '"'"'sudo -H -S -n -u root /bin/sh -c '"'"'"'"'"'"'"'"'echo BECOME-SUCCESS-eeupfcbxwikueumlwnwhgypzlivbursu; /usr/bin/python'"'"'"'"'"'"'"'"' && sleep 0'"'"''
Escalation succeeded
<192.168.0.101> (0, b'\n{"changed": true, "end": "2019-12-21 14:51:55.873596", "stdout": "Listing...", "cmd": "apt list --upgradable", "rc": 0, "start": "2019-12-21 14:51:55.732134", "stderr": "\\nWARNING: apt does not have a stable CLI interface. Use with caution in scripts.", "delta": "0:00:00.141462", "invocation": {"module_args": {"creates": null, "executable": null, "_uses_shell": true, "_raw_params": "apt list --upgradable", "removes": null, "argv": null, "warn": true, "chdir": null, "stdin": null}}}\n', b'OpenSSH_7.9p1 Debian-10+deb10u1, OpenSSL 1.1.1d  10 Sep 2019\r\ndebug1: Reading configuration data /etc/ssh/ssh_config\r\ndebug1: /etc/ssh/ssh_config line 19: Applying options for *\r\ndebug2: resolve_canonicalize: hostname 192.168.0.101 is address\r\ndebug1: auto-mux: Trying existing master\r\ndebug2: fd 3 setting O_NONBLOCK\r\ndebug2: mux_client_hello_exchange: master version 4\r\ndebug3: mux_client_forwards: request forwardings: 0 local, 0 remote\r\ndebug3: mux_client_request_session: entering\r\ndebug3: mux_client_request_alive: entering\r\ndebug3: mux_client_request_alive: done pid = 6894\r\ndebug3: mux_client_request_session: session request sent\r\ndebug3: mux_client_read_packet: read header failed: Broken pipe\r\ndebug2: Received exit status from master 0\r\n')
ok: [pihole.local] => changed=false
  cmd: apt list --upgradable
  delta: '0:00:00.141462'
  end: '2019-12-21 14:51:55.873596'
  invocation:
    module_args:
      _raw_params: apt list --upgradable
      _uses_shell: true
      argv: null
      chdir: null
      creates: null
      executable: null
      removes: null
      stdin: null
      warn: true
  rc: 0
  start: '2019-12-21 14:51:55.732134'
  stderr: |2-

    WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
  stderr_lines:
  - ''
  - 'WARNING: apt does not have a stable CLI interface. Use with caution in scripts.'
  stdout: Listing...
  stdout_lines: <omitted>

TASK [save variable as ansible fact] **********************************************************************************************************************************************************************************************
task path: /home/ansible/provisioning/playbooks/update_packages.yml:42
ok: [pihole.local] => changed=false
  ansible_facts:
    UPGRADEABLE_PACKAGES_LIST: Listing...

TASK [list upgradeable packages] **************************************************************************************************************************************************************************************************
task path: /home/ansible/provisioning/playbooks/update_packages.yml:48
ok: [pihole.local] =>
  UPGRADEABLE_PACKAGES_LIST: Listing...

TASK [waiting for input] **********************************************************************************************************************************************************************************************************
task path: /home/ansible/provisioning/playbooks/update_packages.yml:54
[waiting for input]
check, if there are any critical updates. press 'enter' to continue, 'ctrl+c' to abort:
[...]
```
````

##### GitLab
````
<!-- INSTRUCTIONS
What not to report
- Bugs in unofficial builds or anything not downloaded from our official portal
- Missing Builds
- Problems with the website
- Asking for device support
- Feature requests

If you need help please see https://www.lineageos.org/community/

Anything between <!- - and - -> won't be shown when your issue is created.
-->

## Expected Behavior
<!--- Tell us what should happen -->
The developer option `Mobile data always active` should be `turned off by default` for future `LineageOS image releases` in order to extend battery life.

## Current Behavior
The current image `LinageOS 18.1 (lineage-18.1-20210401-nightly-bacon-signed.zip)` activates this developer option automatically, which drains the battery faster. After estimated `one and a half days` the battery is empty. Disabling it, increases the battery life `up to four days`.

## Possible Solution
Workaround: Disabling it after each LineageOS update.

## Steps to Reproduce
<!--- Provide a link to a live example, or an unambiguous set of steps to -->
<!--- reproduce this bug. Include code to reproduce, if relevant -->
Assuming, that `Developer options` has been activated before.
1. Go to `Settings - System - Developer options`
2. Scroll down to `Mobile Data always active`
3. `Mobile Data always active` should be enabled
4. Disable the option.

<!-- THIS SECTION IS MANDATORY. If it is not filled out correctly, your issue will be marked as invalid. -->
<!-- Example:

/device mako (found at https://wiki.lineageos.org/devices/)
/version lineage-15.1
/date 2017-12-15
/kernel
/baseband
/mods Google Apps, F-Droid
-->


/device bacon
/version lineage-18.1
/date 20210401
/kernel
/baseband
/mods none

<!-- Replace the following line with "I have read the directions" -->
I have read the directions.
````
