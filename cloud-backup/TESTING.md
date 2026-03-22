# Testing Guide for Cloud Backup Script (v1.0)

**Status:** ✅ Stable

This version does **not** use the old `status`, `schedule`, `retention`, `field`, or `backup` commands from earlier drafts. The implemented command surface is:

- `!cloudbackup help`
- `!cloudbackup list`
- `!cloudbackup setup <provider>`
- `!cloudbackup remove <profile>`
- `!cloudbackup sync`

It also includes automatic sync behavior controlled by script configuration values near the top of the file.

---

## ✅ Prerequisites

Before testing, ensure:

1. **auto-mcs** is installed and running
2. **AMscript** is enabled
3. A Minecraft server exists in auto-mcs
4. The server has at least one local backup for sync testing
5. The server has internet access for:
   - downloading `rclone` automatically
   - authorizing cloud providers
   - syncing to remote storage

---

## ⚙️ Configuration Used by This Script

These values are configured directly in the script:

```python
auto_sync: bool = True
sync_interval: int = 5
root_folder: str = 'auto-mcs'
retention_limit: int | str | None = None
```

### Notes

* `auto_sync = True` enables automatic sync behavior
* `sync_interval` is measured in **minutes**
* `root_folder` controls the root remote directory
* `retention_limit` controls remote cleanup behavior:

  * `None` → mirrors local backup maximum
  * `int` → keep that many remote backups
  * `'unlimited'` → disables retention purge

---

## 📥 Script Installation

### Windows

```cmd
copy cloud-backup.ams %appdata%\.auto-mcs\Tools\amscript\
```

### Linux/macOS

```bash
cp cloud-backup.ams ~/.auto-mcs/Tools/amscript/
```

Or alternatively, drag-n-drop into the script manager

Reload:

```text
!ams reload
```

---

## 🧪 Test Cases

## 1. Script Initialization

Reload the script or restart the server.

### Validate

* The script initializes without syntax/runtime errors
* `initialize()` runs via `server.after(0, initialize)`
* OS and architecture are detected correctly
* `RCloneManager()` is created
* If `auto_sync` is enabled, the script attempts an initial sync pass

### Expected behavior

* No crash during load
* If no provider is configured yet, the console may warn that syncing requires a cloud backend
* If a provider is already configured, the script may begin syncing pending backups automatically

---

## 2. rclone Auto-Installation

This script attempts to install `rclone` automatically if it is missing and the server is online.

### Test

Remove or rename the existing `rclone` binary if present, then reload the script.

### Validate

* The script checks GitHub connectivity
* The correct archive is selected for the current OS/architecture
* `rclone` is downloaded into `server.util_path/rclone`
* The binary is extracted and made executable on Unix-based systems
* The script continues initialization successfully

### Failure case

If the server is offline:

* installation should fail gracefully
* commands that depend on `rclone` should not succeed

---

## 3. Help Command

Run:

```text
!cloudbackup help
```

### Validate

The help output includes exactly these subcommands:

* `!cloudbackup help`
* `!cloudbackup list`
* `!cloudbackup setup <provider>`
* `!cloudbackup remove <provider>`
* `!cloudbackup sync`

### Expected behavior

* Command descriptions are shown
* No references to removed or nonexistent commands

---

## 4. List Command

Run:

```text
!cloudbackup list
```

### Validate

* The script displays all available `rclone` backends from `rclone help backends`
* The script also displays configured profiles from `rclone.conf` if any exist
* If no profiles are configured, it displays that none are configured

### Expected behavior

* Available backends are listed by backend key
* Configured profiles are shown as:

  * `<profile> (<backend>)`

---

## 5. Setup Command (Interactive Authorization)

Run:

```text
!cloudbackup setup gdrive
```

You can also test with other backends such as:

```text
!cloudbackup setup onedrive
!cloudbackup setup dropbox
!cloudbackup setup mega
!cloudbackup setup s3
```

### Validate

* The backend name is fuzzy-matched against available `rclone` backends
* The script starts `rclone authorize <backend> --auth-no-open-browser`
* An authorization URL is surfaced to the user
* On successful auth, the script creates or updates a matching profile in `rclone.conf`
* The profile is immediately usable for sync
* If `auto_sync` is enabled, `run_sync(player)` is triggered after successful authorization

### Important restriction

This command only works when:

* run from the server console, or
* run by a player on the same machine as the server (`127.0.0.1`-style local access)

### Negative tests

#### No backend specified

```text
!cloudbackup setup
```

Expected:

* error indicating no backend was specified

#### Unknown backend

```text
!cloudbackup setup madeup-provider
```

Expected:

* error indicating the backend is unknown
* suggestion to run `!cloudbackup list`

#### Non-local player authorization attempt

Run `!cloudbackup setup gdrive` from a remote player connection.

Expected:

* error indicating the command only runs on the same device as the server

#### Timeout case

Begin setup but do not complete browser authorization.

Expected:

* after about 120 seconds, authorization times out
* the script terminates the job cleanly
* an error is shown to the user

---

## 6. Remove Command

First, ensure one or more profiles exist.

Run:

```text
!cloudbackup remove <profile>
```

Example:

```text
!cloudbackup remove gdrive
```

### Validate

* Profile matching is fuzzy, based on section names in `rclone.conf`
* The profile section is removed from config
* Config is written back to disk
* `server.persistent['last-cloud-sync']` is cleared
* Internal sync cache is reset

### Negative tests

#### No profile specified

```text
!cloudbackup remove
```

Expected:

* error indicating no profile was specified

#### Unknown profile

```text
!cloudbackup remove not-a-real-profile
```

Expected:

* error indicating no match was found
* suggestion to run `!cloudbackup list`

---

## 7. Manual Sync Command

Run:

```text
!cloudbackup sync
```

### Validate

* The script loads configured profiles
* Only backups not already recorded in `last-cloud-sync` are considered pending
* Each pending backup is copied to each configured profile
* Remote destination path format is:

```text
<profile>:<root_folder>/<server.name>/<backup_filename>
```

### Expected behavior

* If profiles are configured and backups are pending, sync runs
* After successful sync, purge runs automatically
* `server.persistent['last-cloud-sync']` is updated with the successful backup paths

### Negative tests

#### No configured profiles

```text
!cloudbackup sync
```

Expected:

* user-facing error indicating a cloud backend must be added first

#### Nothing to sync

Run sync twice in a row without creating a new local backup.

Expected:

* second run reports that remote is already up to date
* no duplicate upload occurs

#### Job already active

Trigger a sync while an authorization, sync, or purge job is already active.

Expected:

* error indicating a job is active
* second action does not start

---

## 8. Automatic Minute-Based Sync

This script uses:

```python
@server.on_loop(interval=1, unit='minute')
```

and checks whether at least `sync_interval` minutes have elapsed since `last_synced`.

### Test

Set:

```python
auto_sync = True
sync_interval = 1
```

Reload the script, then create a fresh backup and wait.

### Validate

* Automatic sync runs once the interval passes
* Sync does not run continuously every minute unless enough time has elapsed
* `last_synced` is updated after each sync attempt

### Negative test

Set:

```python
auto_sync = False
```

Reload and wait.

Expected:

* no automatic minute-based sync occurs

---

## 9. Sync on Server Stop

This script uses:

```python
@server.on_stop(data, delay=0)
```

and syncs on shutdown only when:

* `auto_sync` is enabled
* `restart` is `False`

### Test A — Normal stop

With `auto_sync = True`, stop the server normally.

### Validate

* `run_sync()` is called
* pending backups are synced before shutdown completes

### Test B — Restart

Trigger a server restart rather than a stop.

### Validate

* the shutdown sync path is skipped when `data['restart'] == True`

---

## 10. Retention / Purge Behavior

Retention is not controlled by commands in this version. It is controlled by:

```python
retention_limit
```

### Test A — Explicit numeric retention

Set:

```python
retention_limit = 3
```

Create and sync more than 3 backups.

### Validate

* after sync, the script lists remote backups using `rclone lsl`
* only `.amb` and `.tgz` files are considered
* backups older than the newest 3 are deleted
* a purge success message is logged

### Test B — Unlimited retention

Set:

```python
retention_limit = 'unlimited'
```

Sync multiple backups.

### Validate

* sync succeeds
* no purge deletes remote backups

### Test C — Mirror local maximum

Set:

```python
retention_limit = None
```

Configure the server’s local backup maximum to a known number and sync multiple backups.

### Validate

* remote purge mirrors the local retention maximum

### Test D — Empty remote

Run purge when the remote folder is empty.

### Validate

* no crash
* player-facing message indicates no remote backups were found

---

## 11. Root Folder Behavior

Set:

```python
root_folder = 'auto-mcs'
```

Then test again with:

```python
root_folder = 'path/to/folder'
```

### Validate

Remote path changes accordingly:

```text
<profile>:auto-mcs/<server.name>/
```

or

```text
<profile>:path/to/folder/<server.name>/
```

---

## 12. Multi-Profile Sync

Configure two or more cloud profiles.

Run:

```text
!cloudbackup sync
```

### Validate

* each pending backup is uploaded to **every configured profile**
* success/failure is tracked per profile
* successful backups are still added to `last-cloud-sync` unless they failed

---

## 13. Config Persistence

After setup, restart the server and run:

```text
!cloudbackup list
```

### Validate

* configured profiles are still present
* the script reloads `rclone.conf`
* sync cache from `server.persistent['last-cloud-sync']` is preserved unless explicitly cleared by removal

---

## 14. Backend Search Quality

Test fuzzy matching in setup:

```text
!cloudbackup setup google drive
!cloudbackup setup nextcloud
!cloudbackup setup onedrive
```

### Validate

* common and slightly fuzzy names resolve correctly when score is high enough
* invalid/low-confidence names do not resolve

---

## 15. Profile Search Quality

After configuring profiles with names like:

* `gdrive-main`
* `onedrive-backups`

Test:

```text
!cloudbackup remove gdrive
!cloudbackup remove onedrive
```

### Validate

* fuzzy matching resolves the intended profile
* the correct section is removed

---

## 16. Local Backup Change Detection

The script uses `server.persistent['last-cloud-sync']` to avoid re-uploading already-synced backups.

### Test

1. Sync current backups
2. Run `!cloudbackup sync` again without creating new backups
3. Create a new local backup
4. Run `!cloudbackup sync` again

### Validate

* Step 2 uploads nothing
* Step 4 uploads only the new backup

---

## 17. Error Handling — Offline State

Disconnect internet or block outbound access, then test:

* script initialization
* setup
* sync

### Validate

* initialization handles lack of connectivity cleanly
* setup cannot complete authorization
* sync fails gracefully without corrupting internal state

---

## 18. Error Handling — rclone Job Contention

While one of these is running:

* authorization
* sync
* purge

attempt to start another command.

### Validate

* the script rejects the new request
* no overlapping job corrupts config or sync state

---

## 19. Console vs Player Behavior

Test commands both:

* from the server console
* from an in-game op player

### Validate

* help/list/sync/remove work in both contexts where applicable
* setup behaves differently:

  * console may open the browser automatically
  * local player gets a clickable auth link
  * remote player is blocked

---

## 20. Large Backup Test

Use a large backup file and run:

```text
!cloudbackup sync
```

### Validate

* upload completes successfully
* no truncation or early termination occurs
* purge still runs after sync if applicable

---

## 🐛 Common Issues to Validate

### Script still “initializing”

Try issuing a command immediately after reload.

Expected:

* commands other than `help` may show:

  * `[cloud-backup] initializing, try again shortly`

### No configured profiles

Expected:

* sync warns that a backend must be added first

### Unknown backend

Expected:

* setup rejects it cleanly

### Unknown profile

Expected:

* remove rejects it cleanly

### Authorization from remote player

Expected:

* blocked for safety/usability reasons

---

## ✅ Final Test Checklist

* [ ] Script loads without errors
* [ ] OS/architecture detection works
* [ ] rclone auto-install works
* [ ] help command matches actual command set
* [ ] list command shows backends and configured profiles
* [ ] setup works for at least one real provider
* [ ] authorization timeout is handled cleanly
* [ ] remove deletes the intended profile
* [ ] manual sync uploads pending backups
* [ ] duplicate sync is prevented by `last-cloud-sync`
* [ ] automatic minute-based sync works
* [ ] stop-time sync works on normal stop
* [ ] stop-time sync is skipped on restart
* [ ] numeric retention purge works
* [ ] unlimited retention disables purge
* [ ] `None` retention mirrors local backup maximum
* [ ] multi-profile sync works
* [ ] config persists across restart
* [ ] backend/profile fuzzy matching works
* [ ] job contention is handled safely
* [ ] large backup sync completes successfully

---

## 📊 Suggested Real-World Validation Matrix

### Providers

Test at least:

* [ ] Google Drive
* [ ] OneDrive
* [ ] Dropbox
* [ ] S3-compatible backend
* [ ] One WebDAV/Nextcloud-style backend

### Platforms

Test at least:

* [ ] Windows
* [ ] macOS
* [ ] Linux
* [ ] Docker / headless environment if relevant

---

## Summary

This script is validated when the following are all true:

1. It can initialize and provision `rclone`
2. It can authorize and store one or more cloud profiles
3. It can sync pending backups to all configured remotes
4. It does not re-upload unchanged backups
5. It purges remote backups correctly according to `retention_limit`
6. It handles shutdown sync correctly
7. It recovers cleanly across restarts

Once all of the above pass, Cloud Backup v1.0 can be considered fully tested against the behavior actually present in the script.
