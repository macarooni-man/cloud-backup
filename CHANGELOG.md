# Changelog

All notable changes to the Cloud Backup amscript project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Planned
- Mega upload implementation (`upload_to_mega()` function)
- Filen upload implementation (`upload_to_filen()` function)
- Automatic backup upload scheduling system
- Backup retention policies (max backups, auto-cleanup)
- Multiple backup destinations support
- Pre-upload encryption
- Backup verification and integrity checks
- S3-compatible storage support (AWS, MinIO, Backblaze)
- Automatic Python dependency installation

---

## [0.2-pre] - 2025-11-07

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