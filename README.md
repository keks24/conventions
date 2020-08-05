# conventions
conventions i use to be consistent as much as possible.

# git conventions
## commits
* use the `english language`
* use `imperative` and `present tense`
* use `commas` as delimiter

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
#3/feature/client_zsh_config
```

# editing configuration files
* always keep the default by `commenting it`

```no-highlight
<comment_sign> custom - YYYYMMDD - <issue_number> - <first_letter_of_firstname><lastname>: <short_description>
```

example:
```no-highlight
#COMMON_FLAGS="-O2 -pipe"
# custom - 20200805 - #2 - rfischer: set "-march" and "-mtune" to "ivybridge", set "-ftree-vectorize" and use "-O2"
COMMON_FLAGS="-ftree-vectorize -O2 -pipe -march=ivybridge -mtune=ivybridge"
```
