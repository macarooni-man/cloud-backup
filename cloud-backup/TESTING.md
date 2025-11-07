# Testing Guide for Cloud Backup Script

## ✅ Prerequisites

Before testing, ensure:
1. **auto-mcs** is installed and running
2. **AMscript** is enabled in auto-mcs
3. You have a Minecraft server created in auto-mcs

## 📦 Installation

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
Run: !cloudbackup setup
```

## 🧪 Test Cases

### 1. Test Help Command
```
!cloudbackup help
```

Expected output:
- List of available commands
- Usage examples

### 2. Test Google Drive Setup

#### Prerequisites
- Create a Google Cloud project
- Enable Google Drive API
- Create a Service Account
- Download the JSON credentials file
- Upload the JSON file to your server (e.g., `/home/user/service-account.json`)

#### Setup Steps
```
!cloudbackup setup gdrive
```

Expected output:
- Provider instructions with pip install command
- List of required fields with descriptions
- Example values

```
!cloudbackup field credentials_file /path/to/service-account.json
```

Expected output:
- ✓ Confirmation that field was set
- Automatic validation that file exists
- "All fields filled! Finalizing setup..." message
- "Cloud backup setup completed."

#### Verify
```
!cloudbackup status
```

Expected output:
```
Provider: gdrive
Settings: {'credentials_file': '/path/to/service-account.json'}
```

### 3. Test Mega Setup

```
!cloudbackup setup mega
```

Expected output:
- Mega setup instructions
- Required fields: email, password

```
!cloudbackup field email your@email.com
```

Expected output:
- ✓ Confirmation
- Next field prompt (password)

```
!cloudbackup field password YourSecurePassword123
```

Expected output:
- ✓ Confirmation
- "All fields filled! Finalizing setup..."

### 4. Test Filen Setup

```
!cloudbackup setup filen
```

```
!cloudbackup field api_token your_filen_api_token_here
```

```
!cloudbackup field folder_id none
```

### 5. Test Progress Command

During setup (after filling first field):
```
!cloudbackup progress
```

Expected output:
```
Setup Progress: 1/2 fields completed
Completed: email
Missing: password
```

### 6. Test Field Help

```
!cloudbackup help credentials_file
```

Expected output:
- Field description
- Example value

### 7. Test Invalid Field

```
!cloudbackup field invalid_field test_value
```

Expected output:
```
Unknown field: invalid_field
Expected fields: email, password
```

### 8. Test File Validation (Google Drive)

```
!cloudbackup setup gdrive
!cloudbackup field credentials_file /nonexistent/file.json
```

Expected output:
```
Credentials file not found: /nonexistent/file.json
Please provide the full path to your Google Service Account JSON file.
```

### 9. Test Backup Command (Placeholder)

After setup is complete:
```
!cloudbackup backup
```

Expected output (current version):
```
Pretending to upload to [provider] (logic coming next).
```

Or if libraries missing (for gdrive):
```
Missing Google Drive libraries. See setup instructions.
```

## 🐛 Common Issues

### Issue: Script not loading
**Solution**: Check auto-mcs logs for Python syntax errors

### Issue: "server" is not defined
**Solution**: This is normal in VSCode - the `server` object is provided by amscript runtime

### Issue: Template download fails
**Solution**: Check internet connection and verify GitHub URL in script:
```python
GITHUB_TEMPLATES_BASE = "https://raw.githubusercontent.com/kalashnikxvxiii-collab/amscripts/main/cloud-backup/templates"
```

### Issue: Google Drive libraries missing
**Solution**: Install required libraries:
```bash
# Windows
pip install --target "%appdata%\.auto-mcs\Tools\amscript\libs" google-api-python-client google-auth google-auth-httplib2 google-auth-oauthlib

# Linux
pip install --target ~/.auto-mcs/Tools/amscript/libs google-api-python-client google-auth google-auth-httplib2 google-auth-oauthlib

# macOS
pip install --target "~/Library/Application Support/auto-mcs/Tools/amscript/libs" google-api-python-client google-auth google-auth-httplib2 google-auth-oauthlib
```

## 📝 Test Checklist

- [ ] Script loads without errors
- [ ] Help command works
- [ ] Can start setup for all providers (gdrive, mega, filen)
- [ ] Provider instructions display correctly
- [ ] Field descriptions and examples show
- [ ] Can set field values
- [ ] Progress command shows correct status
- [ ] Field validation works (file existence check for gdrive)
- [ ] Setup finalizes and saves config
- [ ] Status command shows correct configuration
- [ ] Can restart server and config persists
- [ ] Backup command runs (even if placeholder)

## 🎯 Next Steps After Testing

Once basic setup flow is verified:
1. Implement actual upload logic for each provider
2. Test real backup uploads
3. Implement backup scheduling
4. Add error handling for upload failures
5. Implement backup retention policies
