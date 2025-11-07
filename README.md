# amscripts Collection

A collection of amscripts for **auto-mcs** - the automated Minecraft server manager.

> **auto-mcs** by Kaleb Efflandt: [github.com/macarooni-man/auto-mcs](https://github.com/macarooni-man/auto-mcs)

---

## 📦 Available Scripts

### Cloud Backup (v0.2-pre)

Upload server backups automatically to cloud storage providers.

**Status**: ✅ Setup flow functional | 🚧 Uploads in development

**Supported Providers**:
- ✅ **Google Drive** (implemented)
- 🚧 **Mega** (coming soon)
- 🚧 **Filen** (coming soon)

**Key Features**:
- Interactive guided setup with field validation
- Template-based configuration downloaded from GitHub
- Built-in help system and progress tracking
- Automatic library dependency detection
- Secure credential storage in server directory

**Documentation**: [cloud-backup/README.md](cloud-backup/README.md)

---

## 📁 Repository Structure

```
amscripts/
├── cloud-backup/           # Cloud backup script
│   ├── cloud_backup.ams    # Main script file
│   ├── templates/          # Provider configuration templates
│   │   ├── gdrive_template.json
│   │   ├── mega_template.json
│   │   └── filen_template.json
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
!cloudbackup field credentials_file /path/to/service-account.json
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
- Internet connection (template downloads, uploads)
- Provider account (Google Drive, Mega, or Filen)
- Provider-specific Python libraries (installed via pip)
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
- **Version**: 0.2-pre
- **Status**: Pre-release (setup functional, uploads in development)
- **Last Updated**: 2025-11-07

**Completed**:
- ✅ Core script architecture with amscript API
- ✅ Interactive setup flow with validation
- ✅ Template system with GitHub integration
- ✅ Google Drive upload implementation
- ✅ Help and progress tracking commands
- ✅ Comprehensive documentation

**In Progress**:
- 🚧 Mega upload implementation
- 🚧 Filen upload implementation

**Planned**:
- ⏳ Automatic backup scheduling
- ⏳ Backup retention policies
- ⏳ Multiple backup destinations
- ⏳ Pre-upload encryption
- ⏳ S3-compatible storage support

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

*Last updated: 2025-11-07*