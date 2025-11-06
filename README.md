# AMSCRIPTS

A collection of amscripts for auto-mcs.
This repo currently includes:

- **cloud-backup** – uploads auto-mcs backups to cloud providers (Google Drive, Mega, Filen). Pre-release.

Each script has its own folder and may include configuration files, templates or docs.

## cloud-backup

Location: `cloud-backup/`

On first run this script guides you through a setup process:
- `!cloudbackup setup`
- `!cloudbackup setup gdrive|mega|filen`
- `!cloudbackup set <field> <value>`
- `!cloudbackup finalize`

Templates for provider credentials are in:
`cloud-backup/templates/`
