# Cloud Backup for auto-mcs (amscript)
**Version:** 0.2-pre
**Author:** kalashnikxvxiii

Cloud Backup is an amscript that allows Minecraft server owners using **auto-mcs** to automatically upload world backups to a supported cloud storage provider.

Supported providers:
- **Google Drive** (fully implemented)
- **Mega** (coming soon)
- **Filen** (coming soon)

> ⚠️ **Pre-release notice:**
> This project is currently under active development.
> Currently only the setup flow and Google Drive integration are functional.
> Mega and Filen upload implementations are coming next.

---

## ✅ Features

✔ Upload server backups to cloud storage
✔ Choose one of three supported providers
✔ Guided interactive setup with field descriptions and examples
✔ Template-based configuration system downloaded from GitHub
✔ Built-in help system for each configuration field
✔ Progress tracking during setup
✔ Field validation (file existence checks, format validation)
✔ Persistent configuration stored in server directory
✔ Automatic library dependency detection
✔ Modular design for future provider expansions

---

## ✅ Requirements

- **auto-mcs** installed and running
- **amscript enabled** in auto-mcs settings
- Internet connection (for template downloads & uploads)
- **Operator permissions** (commands require OP level)
- Provider-specific Python libraries (installed per-provider, see below)

---

## ✅ Installation

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
Run: !cloudbackup setup
============================================================
```

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

**Step 1: Choose a provider**
```
!cloudbackup setup gdrive
```
or
```
!cloudbackup setup mega
```
or
```
!cloudbackup setup filen
```

This will:
- Download the provider template from GitHub
- Show setup instructions for the selected provider
- Display required fields with descriptions and examples
- Guide you through library installation if needed

**Step 2: Fill required fields**

The script will show you which fields are needed. Fill them one by one:

```
!cloudbackup field <name> <value>
```

Example for Google Drive:
```
!cloudbackup field credentials_file /path/to/service-account-key.json
```

Example for Mega:
```
!cloudbackup field email your@email.com
!cloudbackup field password YourSecurePassword
```

Example for Filen:
```
!cloudbackup field api_token your_api_token_here
!cloudbackup field folder_id your_folder_id_or_none
```

**Step 3: Automatic finalization**

Once all fields are filled, the setup automatically finalizes and saves your configuration!

---

### Check Setup Progress

During setup, check your progress at any time:
```
!cloudbackup progress
```

Shows:
- How many fields are completed
- Which fields are still missing
- Current progress percentage

---

### Get Help for a Specific Field

```
!cloudbackup help <field_name>
```

Example:
```
!cloudbackup help credentials_file
```

Shows:
- Field description
- Example value
- Requirements

---

### Check Current Configuration

```
!cloudbackup status
```

Shows:
- Selected provider
- Current settings (credentials are stored but not displayed)

---

### Force a Backup Upload

```
!cloudbackup backup
```

Immediately triggers a backup upload to your configured provider.

> **Note:** Currently only Google Drive upload is fully implemented. Mega and Filen will return a placeholder message.

---

## ✅ Provider Setup Guides

### Google Drive

**Prerequisites:**
1. Create a Google Cloud project at [console.cloud.google.com](https://console.cloud.google.com)
2. Enable the Google Drive API
3. Create a Service Account
4. Generate and download a JSON key file
5. Upload the JSON file to your server

**Required Python Libraries:**
```bash
# Windows
pip install --target "%appdata%\.auto-mcs\Tools\amscript\libs" google-api-python-client google-auth google-auth-httplib2 google-auth-oauthlib

# Linux
pip install --target ~/.auto-mcs/Tools/amscript/libs google-api-python-client google-auth google-auth-httplib2 google-auth-oauthlib

# macOS
pip install --target "~/Library/Application Support/auto-mcs/Tools/amscript/libs" google-api-python-client google-auth google-auth-httplib2 google-auth-oauthlib
```

**Setup Commands:**
```
!cloudbackup setup gdrive
!cloudbackup field credentials_file /path/to/service-account-key.json
```

The script will automatically:
- Validate the file exists
- Check it's a JSON file
- Save the configuration

---

### Mega (Coming Soon)

**Prerequisites:**
- Mega account with email and password

**Required Python Libraries:**
```bash
pip install --target <libs_folder> mega.py
```

**Setup Commands:**
```
!cloudbackup setup mega
!cloudbackup field email your@email.com
!cloudbackup field password YourPassword123
```

> ⚠️ Upload functionality not yet implemented. Setup will complete but uploads will show a placeholder message.

---

### Filen (Coming Soon)

**Prerequisites:**
- Filen account with API token

**Required Python Libraries:**
```bash
pip install --target <libs_folder> filen-client
```

**Setup Commands:**
```
!cloudbackup setup filen
!cloudbackup field api_token your_api_token_here
!cloudbackup field folder_id none
```

> ⚠️ Upload functionality not yet implemented. Setup will complete but uploads will show a placeholder message.

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
- `provider`: Selected provider name (`gdrive`, `mega`, or `filen`)
- `settings`: Provider-specific configuration (credentials path, tokens, etc.)

**⚠️ Security Notice:** This file contains sensitive credentials. Do not share or commit it to version control!

---

## ✅ Template System

Each provider has a configuration template stored in this repository:

```
/cloud-backup/templates/
├── gdrive_template.json
├── mega_template.json
└── filen_template.json
```

Templates define:
- Required fields
- Field descriptions
- Example values
- Validation rules

When you run `!cloudbackup setup <provider>`, the script automatically downloads the template from:
```
https://raw.githubusercontent.com/kalashnikxvxiii-collab/amscripts/main/cloud-backup/templates/<provider>_template.json
```

---

## ✅ Troubleshooting

### Script doesn't load
- Check auto-mcs logs for errors
- Verify the file is named `cloud_backup.ams` (not `.txt`)
- Ensure AMscript is enabled in auto-mcs settings

### Template download fails
- Check your internet connection
- Verify the GitHub repository URL is accessible
- The script uses: `https://raw.githubusercontent.com/kalashnikxvxiii-collab/amscripts/main/cloud-backup/templates/`

### "Missing Google Drive libraries" error
- Install required libraries using the pip command shown in setup instructions
- Make sure to use `--target` flag to install in the correct location
- Restart auto-mcs after installing libraries

### Credentials file not found (Google Drive)
- Use the full absolute path to your JSON file
- On Windows, use backslashes or forward slashes: `C:\path\to\file.json` or `C:/path/to/file.json`
- Make sure the file exists and is readable

### Setup state lost after restart
- This is expected - setup state is temporary
- Configuration is only saved after setup is finalized
- If interrupted, restart setup with `!cloudbackup setup <provider>`

---

## ✅ Development Status

**Completed:**
- ✅ Core script architecture with amscript API
- ✅ Interactive setup flow with validation
- ✅ Template system with GitHub integration
- ✅ Google Drive upload implementation
- ✅ Help and progress tracking commands
- ✅ Field validation and error handling

**In Progress:**
- 🚧 Mega upload implementation
- 🚧 Filen upload implementation

**Planned:**
- ⏳ Automatic backup upload scheduling
- ⏳ Backup retention policies
- ⏳ Multiple backup destinations
- ⏳ Encryption before upload
- ⏳ Backup verification and integrity checks
- ⏳ S3-compatible storage support (AWS, MinIO, Backblaze)
- ⏳ Automatic Python dependency installation

---

## ✅ Contributing

Contributions are welcome! If you want to help:

1. **Test the current implementation** and report bugs
2. **Implement Mega upload** (`upload_to_mega()` function)
3. **Implement Filen upload** (`upload_to_filen()` function)
4. **Improve error handling** and user feedback
5. **Add new cloud providers**

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