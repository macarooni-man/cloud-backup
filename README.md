# amscripts Collection

A collection of amscripts for **auto-mcs** - the automated Minecraft server manager.

> **auto-mcs** by Kaleb Efflandt: [github.com/macarooni-man/auto-mcs](https://github.com/macarooni-man/auto-mcs)

---

## 📦 Available Scripts

### Cloud Backup (v0.3-pre)

Upload server backups automatically to 40+ cloud storage providers using **rclone**.

**Status**: ✅ Setup and upload functional | Ready for testing

**Supported Providers** (via rclone):
- Google Drive, OneDrive, Dropbox
- Mega, Filen, Box, pCloud
- Amazon S3, MinIO, Backblaze
- Azure Blob Storage
- **40+ total providers!**

**Key Features**:
- Universal setup using rclone (one tool for all providers)
- OAuth authentication handled by rclone (simple setup)
- Interactive guided setup with step-by-step instructions
- No Python library dependencies required
- Remote validation (checks if rclone remote exists)
- Secure credential storage via rclone

**Documentation**: [cloud-backup/README.md](cloud-backup/README.md)

---

## 📁 Repository Structure

```
amscripts/
├── cloud-backup/           # Cloud backup script
│   ├── cloud_backup.ams    # Main script file (v0.3-pre)
│   ├── templates/          # Provider configuration templates
│   │   └── rclone_template.json  # Universal rclone template
│   ├── README.md           # Detailed user guide
│   └── TESTING.md          # Testing instructions
├── CHANGELOG.md            # Version history and changes
└── README.md               # This file
```

---

## 🚀 Quick Start

### Installation

1. **Clone or download this repository**
   ```bash
   git clone https://github.com/kalashnikxvxiii-collab/amscripts.git
   ```

2. **Copy desired script to auto-mcs**

   **Windows:**
   ```cmd
   copy amscripts\cloud-backup\cloud_backup.ams %appdata%\.auto-mcs\Tools\amscript\
   ```

   **Linux:**
   ```bash
   cp amscripts/cloud-backup/cloud_backup.ams ~/.auto-mcs/Tools/amscript/
   ```

   **macOS:**
   ```bash
   cp amscripts/cloud-backup/cloud_backup.ams ~/Library/Application\ Support/auto-mcs/Tools/amscript/
   ```

3. **Reload scripts in auto-mcs**
   ```
   !ams reload
   ```

### Usage

Each script has its own commands and setup process. See individual README files for details.

**Cloud Backup Example:**
```
!cloudbackup help
!cloudbackup setup gdrive
!cloudbackup field remote_name gdrive-backup
```

---

## 📚 Documentation

- **[CHANGELOG.md](CHANGELOG.md)** - Complete version history with all changes
- **[cloud-backup/README.md](cloud-backup/README.md)** - Cloud Backup user guide
- **[cloud-backup/TESTING.md](cloud-backup/TESTING.md)** - Testing guide with test cases

---

## 🛠️ Requirements

### General Requirements
- **auto-mcs** installed and running
- **amscript enabled** in auto-mcs settings
- **Python 3.9+** (bundled with auto-mcs)

### Script-Specific Requirements

See individual script README files for specific dependencies.

**Cloud Backup:**
- **rclone** installed on your system ([rclone.org](https://rclone.org/downloads/))
- Internet connection (template downloads, uploads)
- Provider account (Google Drive, OneDrive, Dropbox, Mega, Filen, S3, etc.)
- Operator permissions for commands

---

## 🐛 Troubleshooting

### Script not loading
- Verify file extension is `.ams` (not `.txt` or `.ams.txt`)
- Check auto-mcs logs for Python errors
- Ensure AMscript is enabled in auto-mcs settings

### Commands not working
- Verify you have operator permissions (`/op <username>`)
- Try reloading scripts: `!ams reload`
- Check script-specific README for command syntax

### Import errors
- Install required Python libraries (see script documentation)
- Use `--target` flag to install in correct location
- Restart auto-mcs after installing libraries

---

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. **Test existing scripts** and report bugs
2. **Improve documentation** with examples and guides
3. **Submit bug fixes** via pull requests
4. **Implement new features** (check CHANGELOG.md for planned features)
5. **Create new scripts** for the collection

### Development Workflow

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Test thoroughly (use TESTING.md guides)
5. Update documentation (README.md, CHANGELOG.md)
6. Commit your changes (`git commit -m 'Add amazing feature'`)
7. Push to the branch (`git push origin feature/amazing-feature`)
8. Open a Pull Request

### Coding Standards

- Follow Python 3.9 syntax (auto-mcs compatibility)
- Use amscript API correctly (see [amscript-docs.md](amscript-docs.md))
- Include docstrings for functions
- Add comments for complex logic
- Test on Windows and Linux when possible

---

## 📊 Project Status

### Cloud Backup
- **Version**: 0.3-pre (rclone)
- **Status**: Pre-release (setup and upload functional, ready for testing)
- **Last Updated**: 2025-11-08

**Completed**:
- ✅ rclone integration (40+ providers supported)
- ✅ Universal upload architecture (no provider-specific code)
- ✅ Interactive setup flow with rclone validation
- ✅ Remote name validation and error handling
- ✅ Upload functionality with timeout handling
- ✅ Provider-specific setup instructions
- ✅ Comprehensive documentation and testing guides

**Major Change from v0.2-pre**:
- 🚀 **Complete rewrite** using rclone instead of provider-specific Python libraries
- 🎯 **40+ providers** now supported (vs 3 in v0.2-pre)
- 🛠️ **Zero Python dependencies** (just rclone binary)
- ⚡ **Simplified setup** (1 field vs 10+ fields for Google Drive)

**Planned**:
- ⏳ Automatic backup scheduling via `@server.on_loop()`
- ⏳ Backup retention policies (max backups, auto-cleanup)
- ⏳ Multiple backup destinations support
- ⏳ Pre-upload encryption option
- ⏳ Backup verification and integrity checks
- ⏳ Email/webhook notifications

---

## 🔗 Links

- **auto-mcs GitHub**: [github.com/macarooni-man/auto-mcs](https://github.com/macarooni-man/auto-mcs)
- **amscript Documentation**: [amscript-docs.md](amscript-docs.md)
- **Issue Tracker**: [github.com/kalashnikxvxiii-collab/amscripts/issues](https://github.com/kalashnikxvxiii-collab/amscripts/issues)

---

## 📄 License

MIT License — free to modify, distribute, and integrate.

See [LICENSE](LICENSE) file for full details.

---

## 👥 Credits

- **auto-mcs** by Kaleb Efflandt
- **Scripts** by kalashnikxvxiii
- **Development assistance** by Claude (Anthropic)

---

## 📞 Support

For issues, questions, or feature requests:
- 🐛 **Bug reports**: Open an issue on GitHub
- 💡 **Feature requests**: Open an issue with [Feature Request] tag
- 📖 **Documentation**: Check script-specific README and TESTING.md files
- 💬 **General questions**: Open a discussion on GitHub

---

*Repository maintained by kalashnikxvxiii*

*Last updated: 2025-11-08*