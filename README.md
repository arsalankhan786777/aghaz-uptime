# aghaz-uptime

Checks that **aghaz.pk**, **admin.aghaz.pk** and **arsalanbrothers.com** are alive,
every ~5 minutes, from GitHub's servers.

## Why this repo exists

The Aghaz platform already has two health watchers, and neither can cover a total outage:

| Watcher | Where it runs | Blind spot |
|---|---|---|
| `cron-syshealth.php` | on the Aghaz server | cannot report that the server itself is dead |
| `pc-watchdog.ps1` | on the owner's PC | only runs while that PC is switched on |

A monitor has to live somewhere other than the thing it is watching. This one does.

## Why it is public

GitHub gives a **private** repo 2,000 free Actions minutes a month, and every scheduled
run bills a minimum of one minute — so a 5-minute check would cost 8,640 minutes, four
times the allowance. A **public** repo has no minute limit.

That is the only reason this is separate from the main codebase. It contains two domain
names that are already public and nothing else: **no source code, no keys, no store data.**

## What counts as "up"

Not just HTTP 200. A dead PHP worker or a half-finished deploy can answer 200 with a blank
body, so a page is healthy only when it is **200**, **a complete document** (closes
`</html>`) and **at least 20 KB**. The two real pages are ~265 KB and ~181 KB, so that
floor cannot be tripped by ordinary content edits.

It retries **3 times over ~40 seconds** before declaring an outage — one dropped packet is
not a failure, and a monitor that cries wolf gets ignored.

## How you get told

A failed run emails the repository owner automatically (GitHub does this for scheduled
workflows). Check the **Actions** tab for the history.

## Maintenance

Effectively none. `keepalive.yml` commits a dated line weekly so GitHub does not disable
the schedule after 60 days of inactivity. If GitHub ever emails you saying the schedule
was disabled, open **Actions → Enable workflow**.
