# Filtering and include/exclude patterns

Duplicacy loads backup filters from the configured filters file; when unset, it uses the `filters` file in the preference directory. See `src/duplicacy_snapshot.go` (`ListLocalFiles`, `ProcessFilterFile`, and `ProcessFilterLines`). Filter files support blank lines, `#` comments, and `@path` to include another filter file. Relative `@path` values are resolved against the including file's directory. Unprefixed rules are treated as `+` include rules, and duplicate rules are ignored.

Rules use `+` to include, `-` to exclude, `i:` for a regular-expression include, and `e:` for a regular-expression exclude. Matching returns on the first matching rule. If nothing matches, a filter set containing only include rules excludes the path; otherwise it includes it. See `src/duplicacy_utils.go` (`MatchPath` and `matchPattern`). Wildcards `*` and `?` are matched against the entire path string; `*` can therefore span `/`.

Filtering happens during directory listing in `src/duplicacy_entry.go` (`ListEntries`). A directory is traversed only if it passes the filter, so an excluded directory prevents matching descendants from being visited. Keep this traversal behavior in mind when changing include rules. The same listing path also handles the no-backup marker and optional platform file-attribute exclusions.
