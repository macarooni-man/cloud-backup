# Cloud Backup 

Pre-release amscript to upload auto-mcs backups to cloud providers: Google Drive, Mega, Filen.

## Repo layout
- cloud-backup/
    - templates/
        - gdrive_template.json
        - mega_template.json
        - filen_template.json
        - README.md
    - cloud_backup.ams
    - README.md

## How it works

On first run the script will prompt you to run `!cloudbackup setup` (server console or OP in-game).
The setup will download the provider template from this repo and guide you to fill the required credentials.
After finalizing, the script writes a credentials JSON into the server folder and enables cloud uploads.

## Next steps

- Implement `upload_to_mega()` and `upload_to_filen()` in `cloud_backup.ams`.
- Test uploads with a sample backup file.
