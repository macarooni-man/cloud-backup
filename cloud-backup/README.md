# Cloud Backup for auto-mcs (amscript)
**Version:** 0.3-pre
**Author:** kalashnikxvxiii

Cloud Backup is an amscript that allows Minecraft server owners using **auto-mcs** to automatically upload world backups to cloud storage using **rclone**.

**Supports 40+ cloud providers including:**
- Google Drive
- Microsoft OneDrive
- Dropbox
- Mega
- Filen
- Amazon S3 / MinIO / Backblaze
- Azure Blob Storage
- Box, pCloud, and many more!

Full list: [rclone.org](https://rclone.org/)

> ⚠️ **Pre-release notice:**
> This project is currently under active development.
> Setup and upload functionality are implemented and ready for testing.

---

## ✅ Features

✔ Upload server backups to 40+ cloud storage providers
✔ Universal setup using rclone (one tool for all providers)
✔ OAuth authentication handled by rclone (no complex credential files)
✔ Guided interactive setup with step-by-step instructions
✔ Automatic rclone installation detection
✔ Remote validation (checks if rclone remote exists)
✔ Persistent configuration stored in server directory
✔ No Python library dependencies required
✔ Provider-specific setup tips for popular services

---

## ✅ Requirements

- **auto-mcs** installed and running
- **amscript enabled** in auto-mcs settings
- **rclone installed** (see installation guide below)
- Internet connection (for uploads)
- **Operator permissions** (commands require OP level)
- Cloud storage account (Google Drive, OneDrive, Dropbox, etc.)

---

## ✅ Installation

### Step 1: Install rclone

**Windows:**
```powershell
# Option 1: Download from https://rclone.org/downloads/
# Option 2: Use winget
winget install Rclone.Rclone
```

**Linux:**
```bash
curl https://rclone.org/install.sh | sudo bash
```

**macOS:**
```bash
brew install rclone
```

### Step 2: Install the script

1. Download `cloud_backup.ams` from this repository
2. Place it in the auto-mcs amscript folder:

   **Windows:**
   ```
   %appdata%\.auto-mcs\Tools\amscript\cloud_backup.ams
   ```

   **Linux:**
   ```
   ~/.auto-mcs/Tools/amscript/cloud_backup.ams
   ```

   **macOS:**
   ```
   ~/Library/Application Support/auto-mcs/Tools/amscript/cloud_backup.ams
   ```

3. Restart auto-mcs or reload scripts from server console:
   ```
   !ams reload
   ```

---

## ✅ First Startup

If the script is correctly installed, the console will show:

```
============================================================
Cloud Backup not configured.
Run: !cloudbackup setup <provider>
============================================================
```

If rclone is not installed, you'll see installation instructions.

---

## ✅ Usage

All commands are executed from the **Minecraft server console** or **in-game with OP permissions**.

### Show Available Commands
```
!cloudbackup help
```

Shows:
- All available commands
- Usage examples
- Provider options

---

### Start Interactive Setup

**Step 1: Choose a provider and start setup**
```
!cloudbackup setup gdrive
```

Replace `gdrive` with your provider choice:
- `gdrive` (Google Drive)
- `onedrive` (Microsoft OneDrive)
- `dropbox` (Dropbox)
- `mega` (Mega)
- `filen` (Filen)
- `s3` (Amazon S3 / MinIO / Backblaze)
- And 30+ more! See [rclone.org](https://rclone.org/)

This will show you:
- rclone installation status
- Step-by-step instructions to configure your remote
- Provider-specific setup tips

**Step 2: Configure rclone remote**

Open a terminal/command prompt and run:
```bash
rclone config
```

Follow the interactive prompts:
1. Choose `n) New remote`
2. Enter a name (e.g., `gdrive-backup`, `onedrive-backup`)
3. Select your provider type from the list
4. Follow OAuth flow (browser will open for authentication)
5. Complete the configuration

**Step 3: Set the remote name in the script**

```
!cloudbackup field remote_name <your-remote-name>
```

Example:
```
!cloudbackup field remote_name gdrive-backup
```

**Step 4: Done!**

Setup automatically completes when you set the remote name. The script validates that the remote exists in rclone.

---


### Check Current Configuration

```
!cloudbackup status
```

Shows:
- Selected provider
- Configured remote name
- rclone installation status

---

### Upload a Backup

```
!cloudbackup backup
```

Uploads the latest backup to your cloud storage at:
```
<remote>:minecraft-backups/<server-name>/
```

The upload may take several minutes depending on backup size and internet speed.

---

## ✅ Quick Setup Examples

### Google Drive

```bash
# 1. Start setup in-game
!cloudbackup setup gdrive

# 2. In terminal, configure rclone
rclone config
# Choose: n) New remote
# Name: gdrive-backup
# Type: drive (Google Drive)
# Follow OAuth flow in browser

# 3. Set remote name in-game
!cloudbackup field remote_name gdrive-backup
```

---

### Microsoft OneDrive

```bash
# 1. Start setup
!cloudbackup setup onedrive

# 2. Configure rclone
rclone config
# Choose: n) New remote
# Name: onedrive-backup
# Type: onedrive
# Follow OAuth flow in browser

# 3. Set remote name
!cloudbackup field remote_name onedrive-backup
```

---

### Dropbox

```bash
# 1. Start setup
!cloudbackup setup dropbox

# 2. Configure rclone
rclone config
# Choose: n) New remote
# Name: dropbox-backup
# Type: dropbox
# Follow OAuth flow in browser

# 3. Set remote name
!cloudbackup field remote_name dropbox-backup
```

---

### Mega

```bash
# 1. Start setup
!cloudbackup setup mega

# 2. Configure rclone
rclone config
# Choose: n) New remote
# Name: mega-backup
# Type: mega
# Enter your Mega email and password

# 3. Set remote name
!cloudbackup field remote_name mega-backup
```

---

### Amazon S3 / MinIO / Backblaze

```bash
# 1. Start setup
!cloudbackup setup s3

# 2. Configure rclone
rclone config
# Choose: n) New remote
# Name: s3-backup
# Type: s3
# Enter access key, secret key, region, endpoint

# 3. Set remote name
!cloudbackup field remote_name s3-backup
```

---

## ✅ Configuration Files

After setup, the configuration is stored in your server directory:

```
<server_directory>/cloud_backup_config.json
```

Example location:
```
%appdata%\.auto-mcs\Servers\MyServer\cloud_backup_config.json
```

This file contains:
- `provider`: Selected provider name (e.g., `gdrive`, `onedrive`)
- `remote_name`: Your rclone remote name

**Note:** Actual credentials are stored securely by rclone in its own configuration, not in this file.

---

## ✅ How It Works

1. **rclone handles authentication**: OAuth, tokens, and API keys are managed by rclone
2. **Universal configuration**: One template works for all 40+ providers
3. **Secure storage**: Credentials stored in rclone's encrypted config file
4. **Simple uploads**: Script uses `rclone copy` command to transfer backups
5. **Provider flexibility**: Easy to switch providers or use multiple remotes

---

## ✅ Troubleshooting

### Script doesn't load
- Check auto-mcs logs for errors
- Verify the file is named `cloud_backup.ams` (not `.txt`)
- Ensure AMscript is enabled in auto-mcs settings

### rclone not found
- Install rclone using the commands in the Installation section
- Verify installation: open terminal and run `rclone version`
- Reload the script after installing: `!ams reload`

### Remote not found error
- List your remotes: `rclone listremotes`
- Verify you're using the exact remote name (case-sensitive)
- Make sure you completed `rclone config` successfully
- Test the remote: `rclone lsd <remote-name>:`

### Upload fails or times out
- Check your internet connection
- Verify the remote is accessible: `rclone lsd <remote-name>:`
- Large backups may take 10+ minutes (timeout is set to 10 minutes)
- Check rclone logs for detailed error messages

### OAuth browser doesn't open during rclone config
- Make sure you're running `rclone config` from a machine with a browser
- For headless servers, use `rclone config` on your local machine, then copy the config file
- rclone config location: `~/.config/rclone/rclone.conf` (Linux/Mac) or `%APPDATA%\rclone\rclone.conf` (Windows)

---

## ✅ Development Status

**Completed:**
- ✅ Core script architecture with amscript API
- ✅ rclone integration for universal provider support
- ✅ Interactive setup flow with validation
- ✅ Remote existence validation
- ✅ Universal upload implementation (40+ providers)
- ✅ Provider-specific setup instructions
- ✅ rclone installation detection

**Planned:**
- ⏳ Automatic backup upload scheduling
- ⏳ Backup retention policies
- ⏳ Multiple backup destinations
- ⏳ Encryption before upload (via rclone crypt)
- ⏳ Backup verification and integrity checks
- ⏳ Progress bars during uploads

---

## ✅ Contributing

Contributions are welcome! If you want to help:

1. **Test with different providers** and report compatibility issues
2. **Improve error handling** and user feedback
3. **Add automatic scheduling** for backup uploads
4. **Implement retention policies** to manage old backups
5. **Improve upload progress** feedback

Please open an issue before starting major work to discuss the approach.

---

## ✅ License

MIT License — free to modify, distribute, and integrate.

See LICENSE file for full details.

---

## ✅ Credits

- **auto-mcs** by Kaleb Efflandt
- **Cloud Backup script** by kalashnikxvxiii

---

## ✅ Support

For issues, questions, or feature requests:
- Open an issue on GitHub
- Check the `TESTING.md` file for detailed testing instructions