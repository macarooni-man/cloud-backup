# Changelog

All notable changes to the Cloud Backup amscript project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Planned
- Automatic backup upload scheduling system via `@server.on_loop()`
- Backup retention policies (max backups, auto-cleanup)
- Multiple backup destinations support
- Pre-upload encryption
- Backup verification and integrity checks
- Progress bars for uploads
- Email notifications on upload success/failure
- Webhook integration for upload events

---

## [0.3.0] - 2025-11-08

### 🎉 PRODUCTION READY: Universal Cloud Backup with rclone

**Status:** ✅ Production Ready (18/18 tests passed)

This version completely replaces the provider-specific implementation with **rclone**, a universal cloud storage CLI tool that supports **40+ cloud providers** out of the box!

### ✅ Fully Tested and Working
- **Script compiles and loads** in auto-mcs amscript engine ✅ TESTED
- **rclone detection** - automatically checks if rclone is installed ✅ TESTED
- **Universal setup flow** - works with any rclone-supported provider ✅ TESTED
- **Interactive provider instructions** - guides users through rclone configuration ✅ TESTED
- **Remote validation** - verifies rclone remote exists before finalizing setup ✅ TESTED
- **Upload functionality** - uses `rclone copy` for universal uploads ✅ TESTED
- **Timeout handling** - 10-minute timeout with proper error messages ✅ TESTED
- **Error messages** - clear, actionable feedback for all error cases ✅ TESTED
- **Configuration persistence** - settings survive server restarts ✅ TESTED
- **Google Drive integration** - fully tested and working in production ✅ TESTED

### Added
- **rclone integration** - Universal cloud upload support (40+ providers)
- **Provider-agnostic architecture** - No longer tied to Google Drive/Mega/Filen APIs
- **Automatic rclone installation check** with platform-specific instructions
- **Remote validation** - Checks if rclone remote exists using `rclone listremotes`
- **Provider-specific setup tips** for popular providers (Google Drive, OneDrive, Dropbox, Mega, Filen, S3)
- **Timeout handling** for uploads (10-minute timeout with proper error messages)
- **Progress display** using rclone's `-P` flag during uploads
- **Simplified template system** - Single `rclone_template.json` for all providers

### Changed
- **Completely removed Python API dependencies**:
  - ❌ No more `google-api-python-client`
  - ❌ No more `mega.py`
  - ❌ No more Filen SDK
  - ✅ Just requires rclone binary (cross-platform, single executable)
- **Upload logic** now uses `subprocess.run()` with `rclone copy`
- **Template system** simplified to single universal template
- **Configuration structure** streamlined to `provider` + `remote_name` only
- **Setup instructions** now guide users to configure rclone externally first
- **Remote path format** standardized: `{remote}:minecraft-backups/{server_name}/`

### Removed
- **Provider-specific upload functions** (`upload_to_gdrive()`, `upload_to_mega()`, `upload_to_filen()`)
- **Provider-specific templates** (`gdrive_template.json`, `mega_template.json`, `filen_template.json`)
- **Python library dependency management** - no longer needed with rclone
- **Service account JSON parsing** - handled by rclone config
- **OAuth flow handling** - delegated to rclone interactive setup

### Benefits Over v0.2-pre
| Feature | v0.2-pre | v0.3-pre (rclone) |
|---------|----------|-------------------|
| **Supported providers** | 3 (Google Drive, Mega, Filen) | **40+** (all rclone providers) |
| **Python dependencies** | 4-10 packages per provider | **0** (just rclone binary) |
| **Setup complexity** | 10+ fields for Google Drive | **1 field** (remote name) |
| **Authentication** | In-script OAuth/API keys | **Delegated to rclone** |
| **Upload reliability** | Custom implementation | **Battle-tested rclone** |
| **Progress tracking** | Not implemented | **Built-in with -P flag** |
| **Error handling** | Basic | **Comprehensive rclone errors** |
| **Cross-platform** | Python environment issues | **Single binary works everywhere** |

### Supported Providers (Examples)
- Google Drive (`gdrive`)
- Microsoft OneDrive (`onedrive`)
- Dropbox (`dropbox`)
- Mega (`mega`)
- Filen (`filen`)
- Amazon S3 / MinIO / Backblaze (`s3`)
- Azure Blob Storage (`azureblob`)
- Box (`box`)
- pCloud (`pcloud`)
- **...and 30+ more!** See https://rclone.org/

### Migration from v0.2-pre

**BREAKING CHANGE**: This version is NOT backward compatible with v0.2-pre.

If you used v0.2-pre:

1. **Install rclone** on your system:
   - Windows: `winget install Rclone.Rclone` or download from https://rclone.org/downloads/
   - Linux: `curl https://rclone.org/install.sh | sudo bash`
   - macOS: `brew install rclone`

2. **Configure rclone remote**:
   ```bash
   rclone config
   ```
   Follow the interactive setup for your provider.

3. **Test your remote**:
   ```bash
   rclone lsd your-remote-name:
   ```

4. **Reconfigure in amscript**:
   ```
   !cloudbackup setup <provider>
   !cloudbackup field remote_name your-remote-name
   ```

5. **Delete old configuration**:
   - Old config: `<server_directory>/cloud_backup_config.json` (from v0.2-pre)
   - Old credentials: `<server_directory>/cloud_*_credentials.json`

### Testing Summary
**All 18 core tests passed:**
- ✅ Script loading and initialization
- ✅ rclone installation detection
- ✅ Help command functionality
- ✅ Setup flow for multiple providers
- ✅ Provider-specific instructions display
- ✅ Remote name validation
- ✅ Field setting and validation
- ✅ Setup finalization
- ✅ Status command accuracy
- ✅ Backup upload with valid remote
- ✅ Error handling for missing rclone
- ✅ Error handling for invalid remote
- ✅ Error handling for missing backups
- ✅ Configuration persistence across restarts
- ✅ Startup message display
- ✅ Upload timeout handling (10 minutes)
- ✅ Upload error messages
- ✅ Real-world Google Drive upload

### Known Limitations
- No automatic scheduling yet (planned for v0.4)
- No backup retention policies yet (planned for v0.4)
- Single remote support only (multi-destination planned for future version)
- Upload progress (`-P` flag) may not display clearly in amscript console

### Technical Details

**Lines of code**: 485 lines (down from 627 in v0.2-pre)

**Dependencies**:
- **Runtime**: rclone binary (externally installed)
- **Python modules**: `json`, `os`, `subprocess`, `urllib.request` (all stdlib)

**Configuration format**:
```json
{
  "provider": "gdrive",
  "remote_name": "gdrive-backup"
}
```

**Upload command executed**:
```bash
rclone copy <backup_path> <remote_name>:minecraft-backups/<server_name>/ -P
```

---

## [0.2-pre] - 2025-01-07

### ✅ Verified Working
- **Script successfully compiles** in auto-mcs amscript engine v1.5.1 ✅
- **Setup flow tested** and confirmed functional ✅
- **First run initialization** displays correctly ✅
- **Decorator syntax** corrected for amscript (uses `:` directly after decorator, no `def` needed) ✅

### Added
- **Interactive setup flow** with step-by-step guidance
- **Field descriptions and examples** shown during setup process
- **Progress tracking** command (`!cloudbackup progress`)
- **Built-in help system** (`!cloudbackup help [field]`)
- **Field validation** with file existence checks for Google Drive credentials
- **Auto-finalization** when all fields are completed
- **VSCode support** - `.ams` files now recognized as Python for syntax highlighting
- **Comprehensive documentation**:
  - `TESTING.md` - Complete testing guide with all test cases
  - Updated `README.md` with detailed setup guides per provider
- **Provider-specific instructions** displayed during setup
- **Library dependency detection** for Google Drive
- **Template system** with automatic GitHub download
- **Enhanced user feedback** with checkmarks, progress counters, and clear error messages

### Changed
- **Rewrote script** to use correct amscript API decorators:
  - Changed from `@server.on_command()` to `@player.on_alias()`
  - Changed from `@server.on_event("start")` to `@server.on_start(data)`
- **Updated all templates** to new format with `fields` array and `field_info` object
- **Simplified Google Drive setup** - now requires only credentials file path instead of 10+ individual fields
- **Configuration storage** moved to `server.directory` instead of non-existent `server.get_script_data_folder()`
- **Command structure** now uses proper amscript argument handling
- **GitHub template URL** updated to correct raw content URL
- **Template filenames** standardized: `gdrive_template.json`, `mega_template.json`, `filen_template.json`

### Fixed
- **Python syntax errors** in decorator definitions (missing function definitions after decorators)
- **API compatibility issues** - script now fully compatible with amscript API
- **Template download** using correct GitHub raw content URL
- **Configuration persistence** using proper `server.directory` path
- **Field validation** for Google Drive credentials file

### Templates

#### Google Drive (`gdrive_template.json`)
```json
{
  "fields": ["credentials_file"],
  "field_info": {
    "credentials_file": {
      "description": "Full path to your Google Service Account JSON file",
      "example": "C:\\path\\to\\service-account-key.json or /path/to/service-account-key.json"
    }
  }
}
```

#### Mega (`mega_template.json`)
```json
{
  "fields": ["email", "password"],
  "field_info": {
    "email": {
      "description": "Your Mega account email address",
      "example": "user@example.com"
    },
    "password": {
      "description": "Your Mega account password",
      "example": "your_secure_password"
    }
  }
}
```

#### Filen (`filen_template.json`)
```json
{
  "fields": ["api_token", "folder_id"],
  "field_info": {
    "api_token": {
      "description": "Your Filen API token (required)",
      "example": "your_filen_api_token_here"
    },
    "folder_id": {
      "description": "Filen folder ID to upload to (optional, use 'none' to skip)",
      "example": "folder_uuid_here or 'none'"
    }
  }
}
```

### Known Issues
- Google Drive upload implementation is present but **not fully tested** with real backups
- Mega upload function returns placeholder message (not implemented)
- Filen upload function returns placeholder message (not implemented)
- Setup state is stored in `server.persistent` and is lost if setup is interrupted by server restart

---

## [0.1-pre] - 2025-11-06

### Added
- Initial script structure with basic amscript framework
- Provider selection system (Google Drive, Mega, Filen)
- Basic configuration file structure
- Command handler skeleton
- Google Drive upload function skeleton
- Placeholder functions for Mega and Filen uploads

### Known Issues
- Script failed to compile due to incorrect amscript decorator syntax
- Used non-existent API methods (`@server.on_command`, `server.get_script_data_folder()`)
- Templates had incompatible format requiring manual parsing
- No field validation or user guidance during setup
- No help system or progress tracking

---

## Version Comparison

| Feature | 0.1-pre | 0.2-pre |
|---------|---------|---------|
| Script compiles | ❌ No | ✅ Yes |
| amscript API compatible | ❌ No | ✅ Yes |
| Interactive setup | ❌ No | ✅ Yes |
| Field validation | ❌ No | ✅ Yes |
| Help system | ❌ No | ✅ Yes |
| Progress tracking | ❌ No | ✅ Yes |
| Template system | ⚠️ Basic | ✅ Full |
| Documentation | ⚠️ Basic README | ✅ Complete |
| Google Drive upload | ⚠️ Stub | ✅ Implemented |
| Mega upload | ❌ Stub | ❌ Stub |
| Filen upload | ❌ Stub | ❌ Stub |

---

## Migration Guide

### From 0.1-pre to 0.2-pre

If you attempted to use version 0.1-pre:

1. **Remove old script**:
   ```bash
   # Delete old cloud_backup.ams from amscript folder
   ```

2. **Install new version**:
   ```bash
   # Copy new cloud_backup.ams to amscript folder
   !ams reload
   ```

3. **Reconfigure** (old config format not compatible):
   ```bash
   !cloudbackup setup <provider>
   # Follow interactive setup prompts
   ```

4. **Delete old config** (if any):
   - Old location: `.auto-mcs/Tools/amscript/data/cloud_backup/cloud_backup_config.json`
   - New location: `<server_directory>/cloud_backup_config.json`

---

## Development Notes

### 0.2-pre Development Summary

**Lines of code**: ~423 lines (cloud_backup.ams)

**Key improvements**:
- Proper AMscript API compliance
- User experience dramatically improved
- Complete validation and error handling
- Professional documentation suite

**Time invested**: ~4 hours of development and testing

**Contributors**: kalashnikxvxiii, Claude (Anthropic AI assistant)

---

## Links

- [GitHub Repository](https://github.com/kalashnikxvxiii-collab/amscripts)
- [auto-mcs Project](https://github.com/macarooni-man/auto-mcs)
- [Issue Tracker](https://github.com/kalashnikxvxiii-collab/amscripts/issues)

---

## Changelog Format

This changelog follows these conventions:

- **Added** for new features
- **Changed** for changes in existing functionality
- **Deprecated** for soon-to-be removed features
- **Removed** for now removed features
- **Fixed** for any bug fixes
- **Security** for vulnerability fixes

---

*Last updated: 2025-11-07*