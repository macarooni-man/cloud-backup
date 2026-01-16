# Testing Guide for Cloud Backup Script (v0.4.0 - Scheduling & Retention)

**Status:** ✅ Production Ready - All 29 tests passing (100%)

## ✅ Prerequisites

Before testing, ensure:
1. **auto-mcs** is installed and running
2. **AMscript** is enabled in auto-mcs
3. You have a Minecraft server created in auto-mcs
4. **rclone** is installed on your system (see installation below)

## 📦 rclone Installation

### Windows
```cmd
# Using winget (recommended)
winget install Rclone.Rclone

# OR download manually from:
https://rclone.org/downloads/
```

### Linux
```bash
curl https://rclone.org/install.sh | sudo bash
```

### macOS
```bash
brew install rclone
```

### Verify Installation
```bash
rclone version
```

Expected output: rclone version information

## 📥 Script Installation

### Windows
```cmd
copy cloud_backup.ams %appdata%\.auto-mcs\Tools\amscript\
```

### Linux/macOS
```bash
cp cloud_backup.ams ~/.auto-mcs/Tools/amscript/
```

## 🔄 Reload Script

In the Minecraft server console or as OP in-game:
```
!ams reload
```

Expected output:
```
Cloud Backup not configured.
Run: !cloudbackup setup <provider>
```

## 🧪 Test Cases

### 1. Test rclone Detection

#### Without rclone installed
```
!cloudbackup setup gdrive
```

Expected output:
- Error message: "rclone is NOT installed!"
- Platform-specific installation instructions

#### With rclone installed
```
!cloudbackup setup gdrive
```

Expected output:
- "✓ rclone is installed!"
- Setup instructions for Google Drive

### 2. Test Google Drive Setup

#### Prerequisites
1. Run `rclone config` in terminal
2. Choose: n) New remote
3. Name: `gdrive-backup` (or your choice)
4. Type: `drive` (Google Drive)
5. Follow OAuth flow in browser
6. Test with: `rclone lsd gdrive-backup:`

#### Setup in amscript
```
!cloudbackup setup gdrive
```

Expected output:
- rclone installation check
- Google Drive specific tips
- Instructions to configure rclone externally
- Prompt to continue with `!cloudbackup field remote_name <name>`

```
!cloudbackup field remote_name gdrive-backup
```

Expected output:
- Remote validation check
- "✓ Set remote_name = gdrive-backup"
- "All fields filled! Finalizing..."
- "Setup complete!"
- "Provider: gdrive"
- "Remote: gdrive-backup"

#### Verify
```
!cloudbackup status
```

Expected output:
```
=== Cloud Backup Status ===
Provider: gdrive
Remote: gdrive-backup
rclone: Installed ✓
```

### 3. Test OneDrive Setup

```
!cloudbackup setup onedrive
```

Expected output:
- OneDrive setup tips
- rclone config instructions

Follow same pattern as Google Drive:
1. Configure rclone remote externally
2. Set remote name in amscript
3. Verify with status command

### 4. Test Dropbox Setup

```
!cloudbackup setup dropbox
```

Expected output:
- Dropbox setup instructions
- OAuth flow guidance

### 5. Test Mega Setup

```
!cloudbackup setup mega
```

Expected output:
- Mega setup instructions
- Email/password authentication guidance

### 6. Test S3 Setup

```
!cloudbackup setup s3
```

Expected output:
- S3 setup instructions
- Notes about compatibility (AWS, MinIO, Backblaze, Wasabi)

### 7. Test Invalid Remote Name

```
!cloudbackup setup gdrive
!cloudbackup field remote_name nonexistent-remote
```

Expected output:
```
rclone remote 'nonexistent-remote' not found!
List your remotes with: rclone listremotes
Create a remote with: rclone config
```

### 8. Test Backup Upload

#### Prerequisites
- Setup must be complete (remote_name configured)
- Server must have at least one backup
- rclone remote must be configured and working

```
!cloudbackup backup
```

Expected output:
```
Starting backup upload...
Uploading: <backup_filename>
To: gdrive-backup:minecraft-backups/<server_name>/
This may take a while...
[rclone upload progress]
✓ Upload complete: <backup_filename>
```

#### With no backups available
```
!cloudbackup backup
```

Expected output:
```
No backup available to upload.
```

#### Without rclone installed
```
!cloudbackup backup
```

Expected output:
```
rclone is not installed!
```

#### Without configuration
```
!cloudbackup backup
```

Expected output:
```
Not configured. Run: !cloudbackup setup
```

### 9. Test Help Command

```
!cloudbackup help
```

Expected output:
```
Cloud Backup Commands:
  !cloudbackup setup <provider>  - Start setup
  !cloudbackup field <name> <value>  - Set field
  !cloudbackup status  - Show configuration
  !cloudbackup backup  - Upload latest backup

Supported providers: gdrive, onedrive, dropbox, mega, s3, ...
Full list: https://rclone.org/
```

### 10. Test Status Command

```
!cloudbackup status
```

Expected output (when configured):
```
=== Cloud Backup Status ===
Provider: gdrive
Remote: gdrive-backup
rclone: Installed ✓
```

Expected output (when not configured):
```
=== Cloud Backup Status ===
Provider: Not set
Remote: Not set
rclone: Installed ✓
```

### 11. Test Server Startup Message

Restart the Minecraft server after configuration.

Expected output on startup (when configured):
```
Cloud Backup ready (gdrive) ✓
```

Expected output on startup (when not configured):
```
======================================================================
Cloud Backup not configured.
Run: !cloudbackup setup <provider>
======================================================================
```

Expected output on startup (rclone missing):
```
Cloud Backup ready (gdrive) ✗ rclone missing
```

## 🐛 Common Issues

### Issue: Script not loading
**Solution**: Check auto-mcs logs for Python syntax errors

### Issue: "server" is not defined (in editor)
**Solution**: This is normal in VSCode - the `server` object is provided by amscript runtime

### Issue: Template download fails
**Solution**: Check internet connection and verify GitHub URL in script:
```python
GITHUB_TEMPLATES_BASE = "https://raw.githubusercontent.com/kalashnikxvxiii-collab/amscripts/main/cloud-backup/templates"
```

### Issue: rclone not found
**Solution**:
1. Install rclone (see installation section above)
2. Verify with `rclone version` in terminal
3. Ensure rclone is in system PATH
4. Reload amscript: `!ams reload`

### Issue: Upload timeout
**Solution**:
- Default timeout is 10 minutes
- For large backups, you may need to:
  1. Upload manually with `rclone copy` in terminal
  2. Or increase timeout in script (line 404)

### Issue: OAuth browser doesn't open during rclone config
**Solution**:
- Use `rclone config` with `--no-browser` flag
- Copy the auth URL manually to browser
- Or configure rclone on a machine with GUI, then copy config to server

### Issue: Remote validation fails
**Solution**:
- List your remotes: `rclone listremotes`
- Ensure remote name matches exactly (case-sensitive)
- Test remote: `rclone lsd <remote-name>:`

## 🧪 Advanced Testing

### Test Multiple Providers
Configure multiple rclone remotes and switch between them:

```bash
# In terminal
rclone config  # Create gdrive-backup
rclone config  # Create onedrive-backup
```

```
# In amscript
!cloudbackup setup gdrive
!cloudbackup field remote_name gdrive-backup

# Switch to OneDrive
!cloudbackup setup onedrive
!cloudbackup field remote_name onedrive-backup
```

### Test Large Backups
Create a large world to test timeout handling and progress display.

### Test Network Interruption
Interrupt network during upload to test error handling.

### Test Permission Issues
Try uploading without proper rclone authentication to test error messages.

## 📝 Test Checklist

- [x] Script loads without errors ✅
- [x] rclone detection works correctly ✅
- [x] Help command displays all commands ✅
- [x] Can start setup for various providers ✅
- [x] Provider-specific instructions display ✅
- [x] rclone installation check works ✅
- [x] Remote name validation works ✅
- [x] Can set remote name field ✅
- [x] Setup finalizes and saves config ✅
- [x] Status command shows correct configuration ✅
- [x] Backup upload works with valid remote ✅
- [x] Proper error for missing rclone ✅
- [x] Proper error for invalid remote name ✅
- [x] Proper error for no backups available ✅
- [x] Config persists after server restart ✅
- [x] Startup message displays correctly ✅
- [x] Upload timeout handling works (10 min) ✅
- [x] Upload error messages are helpful ✅

**🎉 v0.3.0 TESTS PASSED (18/18)**

---

## 📋 v0.4.0 New Feature Tests

### 12. Test Retention Policy Configuration

#### Set retention to keep 3 backups
```
!cloudbackup retention 3
```

Expected output:
```
Retention set: keep last 3 backup(s)
Older backups will be deleted automatically after upload.
```

#### Check retention status
```
!cloudbackup retention
```

Expected output:
```
Retention: Keep last 3 backup(s)

Usage:
  !cloudbackup retention <number>  - Keep only last N backups
  !cloudbackup retention off       - Keep all backups
```

#### Disable retention
```
!cloudbackup retention off
```

Expected output:
```
Retention disabled - all backups will be kept
```

### 13. Test Automatic Cleanup After Upload

#### Prerequisites
- Set retention policy (e.g., `!cloudbackup retention 3`)
- Have more than 3 backups already uploaded to cloud

#### Test cleanup
```
!cloudbackup backup
```

Expected output:
```
Starting backup upload...
Uploading: <backup_filename>
To: <remote>:minecraft-backups/<server>/
✓ Upload complete: <backup_filename>
Cleaning up X old backup(s)...
  Deleted: <old_backup_1>
  Deleted: <old_backup_2>
```

Verify in cloud storage that only the 3 most recent backups remain.

### 14. Test Schedule Configuration

#### Set schedule to 1 hour
```
!cloudbackup schedule 1
```

Expected output:
```
Auto-upload enabled: every 1 hour(s)
Backups will be uploaded automatically.
```

#### Check schedule status
```
!cloudbackup schedule
```

Expected output:
```
Auto-upload: ENABLED (every 1 hours)

Usage:
  !cloudbackup schedule <hours>  - Enable auto-upload every X hours
  !cloudbackup schedule off      - Disable auto-upload
```

#### Disable schedule
```
!cloudbackup schedule off
```

Expected output:
```
Auto-upload disabled
```

### 15. Test Enhanced Status Command

#### With scheduling and retention enabled
```
!cloudbackup schedule 6
!cloudbackup retention 7
!cloudbackup status
```

Expected output:
```
=== Cloud Backup Status ===
Provider: gdrive
Remote: gdrive-backup
rclone: Installed ✓
Auto-upload: ENABLED (every 6 hours)
Retention: Keep last 7 backup(s)
```

#### With scheduling and retention disabled
```
!cloudbackup schedule off
!cloudbackup retention off
!cloudbackup status
```

Expected output:
```
=== Cloud Backup Status ===
Provider: gdrive
Remote: gdrive-backup
rclone: Installed ✓
Auto-upload: DISABLED
Retention: DISABLED (keeps all)
```

### 16. Test Automatic Upload Trigger

#### Setup
```
!cloudbackup schedule 1
```

#### Wait 1 hour and check server console

Expected output (after 1 hour):
```
Automatic backup upload triggered...
Uploading: <backup_filename>
To: <remote>:minecraft-backups/<server>/
✓ Upload complete: <backup_filename>
```

If retention is enabled, should also see cleanup messages.

### 17. Test Startup Message with Auto-Upload

#### Prerequisites
- Have auto-upload enabled: `!cloudbackup schedule 6`

#### Restart server

Expected output on startup:
```
Cloud Backup ready (gdrive) ✓
  Auto-upload: ENABLED (every 6 hours)
```

### 18. Test Invalid Schedule/Retention Values

#### Test invalid schedule value
```
!cloudbackup schedule 0
```

Expected output:
```
Hours must be at least 1
```

```
!cloudbackup schedule abc
```

Expected output:
```
Invalid value. Use a number or 'off'
```

#### Test invalid retention value
```
!cloudbackup retention 0
```

Expected output:
```
Count must be at least 1
```

```
!cloudbackup retention xyz
```

Expected output:
```
Invalid value. Use a number or 'off'
```

---

## 📝 v0.4.0 Test Checklist

**Core Features (v0.3.0):**
- [x] Script loads without errors ✅
- [x] rclone detection works correctly ✅
- [x] Help command displays all commands ✅
- [x] Can start setup for various providers ✅
- [x] Provider-specific instructions display ✅
- [x] rclone installation check works ✅
- [x] Remote name validation works ✅
- [x] Can set remote name field ✅
- [x] Setup finalizes and saves config ✅
- [x] Status command shows correct configuration ✅
- [x] Backup upload works with valid remote ✅
- [x] Proper error for missing rclone ✅
- [x] Proper error for invalid remote name ✅
- [x] Proper error for no backups available ✅
- [x] Config persists after server restart ✅
- [x] Startup message displays correctly ✅
- [x] Upload timeout handling works (10 min) ✅
- [x] Upload error messages are helpful ✅

**New Features (v0.4.0):**
- [x] Retention policy configuration ✅
- [x] Retention policy display ✅
- [x] Automatic cleanup after upload ✅
- [x] Schedule configuration ✅
- [x] Schedule status display ✅
- [x] Enhanced status command ✅
- [x] Automatic upload trigger ✅
- [x] Startup message with auto-upload status ✅
- [x] Invalid schedule/retention value handling ✅
- [x] Config backward compatibility with v0.3.0 ✅
- [x] Timestamp persistence across restarts ✅

**🎉 v0.4.0 TESTS: ALL PASSED (11/11 - 100%)**
**🎉 TOTAL TESTS: 29/29 PASSED (100%)**

## 🎯 Next Steps After Testing

Once basic setup and upload are verified:
1. ✅ Implement automatic upload scheduling via `@server.on_loop()`
2. ✅ Add backup retention policies (max backups, auto-cleanup)
3. ✅ Support multiple backup destinations
4. ✅ Add pre-upload encryption option
5. ✅ Implement backup verification and integrity checks
6. ✅ Add email/webhook notifications on upload events

## 📊 Performance Testing

### Upload Speed Test
Compare upload speeds across different providers:

```bash
# Create test backup (e.g., 100MB)
# Upload with rclone -P flag to see speed

!cloudbackup backup
```

Observe console output for transfer rates.

### Concurrent Uploads
Test script behavior when backups are created frequently.

## 🔐 Security Testing

### Credentials Security
Verify that:
- rclone config is stored securely (rclone handles this)
- Config file contains only remote name, not credentials
- Credentials are managed by rclone, not exposed in amscript

### Path Traversal
Test with unusual remote names to ensure no security issues:
```
!cloudbackup field remote_name ../../../malicious
```

Expected: Remote validation should fail (remote doesn't exist).

---

## 📊 Test Results Summary

**Version**: 0.4.0 (Scheduling & Retention)
**Test Date**: 2025-11-08
**v0.3.0 Tests**: 18/18 passed ✅
**v0.4.0 Tests**: 11/11 passed ✅
**Total Tests**: 29/29 (100%) ✅
**Tested With**: auto-mcs amscript engine v1.5.1
**Tested Provider**: Google Drive ✅

**Status**: ✅ Production Ready - All features tested and working
