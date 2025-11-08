# Testing Guide for Cloud Backup Script (v0.3-pre - rclone)

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

- [ ] Script loads without errors
- [ ] rclone detection works correctly
- [ ] Help command displays all commands
- [ ] Can start setup for various providers
- [ ] Provider-specific instructions display
- [ ] rclone installation check works
- [ ] Remote name validation works
- [ ] Can set remote name field
- [ ] Setup finalizes and saves config
- [ ] Status command shows correct configuration
- [ ] Backup upload works with valid remote
- [ ] Proper error for missing rclone
- [ ] Proper error for invalid remote name
- [ ] Proper error for no backups available
- [ ] Config persists after server restart
- [ ] Startup message displays correctly
- [ ] Upload timeout handling works (10 min)
- [ ] Upload error messages are helpful

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

**Version**: 0.3-pre (rclone)
**Last updated**: 2025-11-08
**Tested with**: auto-mcs amscript engine v1.5.1
