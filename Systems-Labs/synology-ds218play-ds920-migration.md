# Synology DS218play → DS920+ Migration

- **Category:** Systems
- **Status:** Completed
- **Created:** 2026-07-23
- **Last updated:** 2026-07-27
- **Completed:** 2026-07-27

## Activities

- Migration
- Synology
- Upgrade

## Tags

- Synology

## Objective / Problem

Migrating my existing Synology DS218play storage environment to a Synology DS920+. The existing NAS contains two 8 TB drives configured as SHR with one-drive fault tolerance and an ext4 filesystem. Approximately 5.6 TB of data is currently stored on the volume. The goal is to migrate the existing drives and data to the DS920+, validate the storage pool and services, perform extended drive health testing, and configure the 1 TB NVMe cache after migration.

## Environment Components

- Synology

## Environment Notes

Source NAS: Synology DS218play
Destination NAS: Synology DS920+

Source DSM:
DSM 7.3.2-86009 Update 4

Storage:
2 × 8 TB HDD
Synology Hybrid RAID (SHR)
1-drive fault tolerance

Volume:
Volume 1 - Media
Filesystem: ext4
Usable capacity: ~7.3 TiB
Used: ~5.6 TB
Free: ~1.5 TB

Drive health:
Drive 1 - Healthy, Quick SMART passed
Drive 2 - Healthy, Quick SMART passed

Backup:
DSM configuration .dss backup exported successfully

Destination:
DS920+
1 TB NVMe cache available
Bays 3 and 4 currently available for future expansion

## Tools

None recorded.

## Steps

### 1. Verify Existing Storage Pool

Reviewed the current storage configuration before migration to establish a known-good baseline.

#### Result

```text
Storage Pool 1: Healthy
RAID Type: Synology Hybrid RAID (SHR)
Protection: 1-drive fault tolerance
Drives: 2 × 8 TB HDD
Usable storage pool capacity: ~7.3 TiB

Volume 1 - Media: Healthy
Used: ~5.6 TB
Available: ~1.5 TB
```

#### Evidence

- [01-source-storage-pool.png](<https://1drv.ms/i/c/54cda586f36f2027/IQAfkYASU8GERq7u5VmPfiPIAR3xN5vzETF-1SoUoNHc-kc?e=7qrLTH>)

### 2. Verify Filesystem

Checked the filesystem used by the existing volume because this affects what will be preserved during HDD migration.

#### Result

```text
Volume: Volume 1 - Media
Filesystem: ext4

The existing ext4 filesystem will remain ext4 during HDD migration. It will not automatically convert to Btrfs on the DS920+.
```

#### Evidence

- [02-source-volume-ext4.png](<https://1drv.ms/i/c/54cda586f36f2027/IQCq9m9G0WIhQJ60DxO75OfVASDRiDNmcw-B5PTxIPR3VMw?e=Pbv8N7>)

### 3. Verify DSM Version

Verified the DSM version on the DS218play before migration.

#### Result

```text
DSM 7.3.2-86009 Update 4
```

#### Evidence

- [03-source-dsm-version.png](<https://1drv.ms/i/c/54cda586f36f2027/IQCN5WqCxlvfRrRfdV0zJ6drAVe10k9PL4zN20pBY16qCKM?e=YGwyve>)

### 4. Export DSM Configuration

Exported the DSM configuration before migration to provide a recovery copy of system settings.

#### Result

```text
DSM configuration backup exported successfully as a .dss file.
```

### 5. Verify Drive Health

Reviewed both 8 TB HDDs before migration. I attempted an extended S.M.A.R.T. test, but it stalled at 10% even after multiple attempts. I completed the Quick S.M.A.R.T. tests on both drives instead.

Extended S.M.A.R.T. testing was deferred until after a successful migration to the DS920+.

#### Result

```text
Drive 1: Healthy
Quick S.M.A.R.T. test: Passed

Drive 2: Healthy
Quick S.M.A.R.T. test: Passed

Extended S.M.A.R.T. testing deferred until after migration to the DS920+.
```

#### Evidence

- [04-source-drive-health.png](<https://1drv.ms/i/c/54cda586f36f2027/IQAAioiwp5NdQKHcKgDCeH2AAeA5xFIBZ6_FOhtRlGKQekY?e=cD6tJ1>)

### 6. Document Existing Shared Folders

Captured the shared-folder configuration so the folders could be verified after migration.

#### Result

```text
Audiobooks
backups
homes
Media
Movies
PlexMediaServer
TV Shows

All shared folders are located on Volume 1 - Media.
```

#### Evidence

- [05-source-shared-folders.png](<https://1drv.ms/i/c/54cda586f36f2027/IQBadIxFl--mSIZ9oN95Bds5AV2LbYgmY-Xi2sVNJEmea7U?e=3bWP4J>)

### 7. Document Installed DSM Packages

Recorded installed packages before migration so services can be validated and repaired if necessary on the DS920+.

#### Result

```text
Synology Application Service*
Synology Photos
QuickConnect
Synology Drive Server
SMB Service
Tailscale
VPN Server
Plex Media Server
Active Insight
Antivirus Essential
File Station
Log Center
OAuth Service
Python2
SAN Manager
Secure SignIn Service
Storage Manager
Universal Search
Universal Viewer
Node.js v20
Node.js v18

*Synology Application Service
Version: 1.8.3-20742
Installed volume: Volume 1
Status: Running
Dependency: Node.js v20
```

#### Evidence

- [06-source-installed-packages.png](<https://1drv.ms/i/c/54cda586f36f2027/IQDyl2TxjutKQZPJ5S5_zEbdAbRFnNBxFkR8p7r6Q-vGuls?e=1W3Y4H>)
- [07-source-synology-application-service.png](<https://1drv.ms/i/c/54cda586f36f2027/IQAlnnAEQUpWToKKmp7ToeIAAUDtroaBqT6Ye_LiEbZpctY?e=1g6gSj>)

### 8. Complete Pre-Migration Validation

Completed pre-migration checks before shutting down the DS218play.

#### Result

```text
SHR storage pool: Healthy
Both 8 TB HDDs: Healthy
Quick SMART tests: Passed
Volume filesystem: ext4
DSM version confirmed
DSM configuration backup completed
Shared folders documented
Installed packages documented

System ready for controlled shutdown and HDD migration.
```

### 9. Shut Down Source NAS

The DS218play was shut down through DSM after all pre-migration validation was completed. Power and network connections were disconnected before any hardware was removed.

#### Result

```text
DS218play shut down cleanly through DSM.
Power disconnected.
Ethernet disconnected.
System ready for physical drive removal.
```

#### Evidence

- [09-source-powered-off DS218play](<https://1drv.ms/i/c/54cda586f36f2027/IQBWZJUrJBtRQJwGzH7dTM-tAbeaeZN9f26OSdH5xuCi4Lw?e=A7Im7C>)

### 10. Open DS218play and Remove Existing Drives

Opened the powered-off DS218play enclosure to access the existing storage drives. The two 8 TB HDDs were identified and labelled before removal so their original drive order could be maintained during installation in the DS920+.

#### Result

```text
DS218play enclosure opened successfully.

Drive 1 and Drive 2 were identified and labelled before removal.

Drive 1: 8 TB HDD removed successfully.
Drive 2: 8 TB HDD removed successfully.

Original drive order preserved for migration to the DS920+.
```

#### Evidence

- [10-source-labelled-drive-1-and-2](<https://1drv.ms/i/c/54cda586f36f2027/IQDDxejgLIItQpfrse0XZb8pATaCqZxmfi2Rq6QCpDHMWHY?e=4H8ywp>)

### 11. Install Existing HDDs in DS920+

Installed the two existing 8 TB HDDs from the DS218play into the DS920+. Original drive order was preserved during the transfer.

#### Result

```text
Drive 1 installed in DS920+ Bay 1.
Drive 2 installed in DS920+ Bay 2.

Bays 3 and 4 remain empty.

Original source-drive order preserved for HDD migration.
DS920+ remained powered off during installation.
```

#### Evidence

- [11-ds920-source-drives-installed.jpg](<https://1drv.ms/i/c/54cda586f36f2027/IQBsIiKuF8yGRpS-AF4GsMmXAdD0fgxzmGmkvFH36J7C9ms?e=vC1giN>)

### 12. First Boot and Discovery of DS920+

Connected the DS920+ to power and the network, then powered it on with the two migrated 8 TB HDDs installed in Bays 1 and 2. Used Synology Assistant to discover the new DS920+ and determine whether migration was possible.

#### Result

```text
Destination NAS: Synology DS920+
Status: Migratable
DSM detected: 7.3.2-86009
Network configuration: DHCP
Assigned IP: 192.168.10.187

Synology Assistant successfully recognized the existing installation and reported the DS920+ as Migratable.

Synology Assistant displayed an informational notice stating that
the operating system version recorded on the destination motherboard
was newer than the version recorded on the migrated drives.

Existing system configuration was recognized as eligible for migration.
```

#### Evidence

- [12-ds920-migratable-detected.png](<https://1drv.ms/i/c/54cda586f36f2027/IQC3Z9_59GZTQa0XdLOLHle8Aexiwooo5ufPWAaOPPJP_Wk?e=wfpAKf>)

### 13. Select HDD Migration Method

The DS920+ detected the existing data and DSM configuration on the transferred drives. The migration wizard offered either retaining the existing configuration or resetting DSM while keeping only the files. The existing configuration was retained.

#### Result

```text
Existing data and system configuration detected.

Selected:
Retain system configurations
(Keep the files and settings)
```

#### Evidence

- [13-ds920-migration-options](<https://1drv.ms/i/c/54cda586f36f2027/IQDINIcJs3MyRaMgqkR82yDKAUgXHEKab2H6bjenN-JGNGk?e=jQpeJv>)

### 14. Select DSM Installation Method

The migration wizard required DSM to be installed on the DS920+. Automatic installation from Synology was selected rather than manually supplying a DSM .pat file.

#### Result

```text
Automatic DSM installation selected.

DSM offered:
7.4.1-90080

Installation source:
Synology website
```

#### Evidence

- [14-ds920-install-DiskStation-Manager](<https://1drv.ms/f/c/54cda586f36f2027/IgAZdMkDyBuQRrSjFP03pOZXAXFAF6GS4g03078nqmBG8VQ?e=4Ya4kz>)

### 15. Complete DSM Migration and Sign In

DSM installation and the migration process completed on the DS920+. After the system rebooted, the existing DSM account credentials were used to sign back into the NAS.

#### Result

```text
DSM migration completed.
DS920+ rebooted successfully.
DSM login became available.
Existing credentials accepted.
DSM desktop accessible.
```

#### Evidence

- [15-ds920-DSM-desktop-post-successful-login](<https://1drv.ms/f/c/54cda586f36f2027/IgAZdMkDyBuQRrSjFP03pOZXAXFAF6GS4g03078nqmBG8VQ?e=8Htg7b>)

### 16. Verify Migrated Storage Pool

Opened Storage Manager immediately after migration to validate the existing SHR storage pool, both migrated HDDs, and Volume 1 before making any additional configuration changes. The original ext4 filesystem was preserved.

#### Result

```text
Storage Pool 1: Healthy
RAID Type: Synology Hybrid RAID (SHR)
Protection: 1-drive fault tolerance
Total capacity: 7.3 TB

Drive 1: Healthy
Drive 2: Healthy

Volume 1 - Media: Healthy
Filesystem: ext4
Used space: 5.7 TB
Free space: 1.5 TB

Existing SHR storage pool and ext4 volume successfully recognized
on the DS920+ after HDD migration.
```

#### Evidence

- [16-ds920-storage-and-filesystem-verified.png](<https://1drv.ms/i/c/54cda586f36f2027/IQCE7AMNlAu1T59HexLeXNurAaFsO15DDc5zwPJ_4PIpopw?e=c7rUss>)

### 17. Verify Migrated Data and Shared Folders

Verified that the shared folders and representative files from the DS218play remained accessible after migration to the DS920+.

#### Result

```text
Existing shared folders were present after migration.

Verified shared-folder inventory:
- Audiobooks
- backups
- homes
- Media
- Movies
- PlexMediaServer
- TV Shows

File Station successfully accessed the migrated Media share.

Representative content verified:
Media → Kids TV Shows

Multiple existing media folders were visible and accessible.

Migration data verification passed.
```

#### Evidence

- [17-ds920-shared-folders-and-data-verified.png](<https://1drv.ms/i/c/54cda586f36f2027/IQBPDHMtPBY3RYxO8Pl5R0UXAarcbc2SC0yxIPHmkBHgSgk?e=u8KutZ>)

### 18. Repair and Review Migrated Packages

Reviewed installed packages after the DS920+ migration. Several packages required repair following the hardware/DSM migration. Plex Media Server did not migrate cleanly and was removed because Plex is already running successfully on the existing Dell OptiPlex server and the DS920+ is not currently planned to serve as the primary Plex host.

#### Result

```text
Post-migration package review completed.

Packages requiring repair:
- Active Insight
- Secure SignIn Service
- Tailscale

All three packages were repaired successfully.

Plex Media Server:
- Did not migrate/update successfully
- Package removed from DS920+
- Existing Plex deployment on Dell OptiPlex remains the primary Plex server

Package cleanup completed after migration.
```

#### Evidence

- [18-post-migration-packages.png](<https://1drv.ms/i/c/54cda586f36f2027/IQAQa0rfWUbZT5qpnfjh5vJIAf7c66AwSX8ZST2H0izI_4c?e=MqeVdB>)

### 19. Verify Tailscale After Migration

Tailscale required package repair following the hardware migration. After repair, the application was opened to confirm that the DS920+ reconnected to the existing tailnet.

#### Result

```text
Tailscale package repaired successfully.

Device identity: djserver
Connection status: Connected
Exit node: None

Existing Tailscale identity was retained after migration.

Tailscale package showed Connected after repair, but initial remote DSM access test over cellular using HTTPS/5001 did not load. Further connectivity testing required.
```

#### Evidence

- [19-tailscale-post-migration-connected.png](<https://1drv.ms/i/c/54cda586f36f2027/IQAmbyDujbUaSqvsZwttBe7-AauPHjnBhSL2jZc7v2E5Rgk?e=OAvfgG>)

### 20. Verify SMB File Sharing

Tested SMB access from a Windows client after migration to confirm that the DS920+ remained accessible through the existing network name and that migrated shares could be browsed normally.

#### Command

```text
\\DJServer
```

#### Result

```text
SMB connectivity verified.

Windows successfully resolved the NAS as DJServer.

Successfully browsed:
DJServer → Media → Kids Movies

Existing folders and media files were visible and accessible.

SMB file sharing passed post-migration validation.
```

#### Evidence

- [20-smb-file-sharing-verified.png](<https://1drv.ms/i/c/54cda586f36f2027/IQDH88gMINidTpaECOfzR5pqAeMv4DcQiazkHsng_Nx01ig?e=HyUyjX>)

### 21. Verify Synology Drive After Migration

Synology Drive Server reported healthy after migration and retained the existing registered clients. However, both the Windows desktop client and Pixel 9 Pro were shown as Offline, indicating that client connectivity did not automatically resume after migration.

The client connection was edited to point directly to the migrated DS920+ using its new LAN IP address.

#### Command

```text
192.168.10.187
```

#### Result

```text
Synology Drive Server: Healthy

Registered clients retained:
- Windows desktop: Offline
- Pixel 9 Pro: Offline

Existing client registrations survived migration,
but post-migration connectivity requires troubleshooting.

Remediation:
Updated the existing Synology Drive Client connection
to use the DS920+ LAN IP address: 192.168.10.187.

Final state:
Existing two-way sync task reconnected successfully.
Synology Drive Client displayed a healthy/connected status.
No sync task recreation was required.
```

#### Evidence

- [21A-synology-drive-client-edit-connection](<https://1drv.ms/i/c/54cda586f36f2027/IQA5XrNvIW9LQI6FgtU5eTaNAQlXajOUmhCA8yHEIDOjTJg?e=3EQxW1>)
- [21B-synology-drive-client-connection-established](<https://1drv.ms/i/c/54cda586f36f2027/IQD8gv6D_E4MQLrnjUPrKOjPAbHOi_elsyAf6C0LNe86S4o?e=RaIBTF>)
- [21-synology-drive-clients-offline](<https://1drv.ms/i/c/54cda586f36f2027/IQBejjA4KMfQRax48cSUEnsQAUM5htUZ6IK1Z_RBoXrSw0U?e=gA9ADg>)

### 22. Restore QuickConnect and Synology Drive Client Access

Restored the existing QuickConnect configuration on the DS920+ using the original QuickConnect ID. Synology Drive clients were then updated to use the QuickConnect ID rather than relying on the newly assigned LAN IP address. This restored connectivity for both the Windows desktop client and Pixel 9 Pro.

#### Result

```text
QuickConnect enabled successfully.
Original QuickConnect ID retained.

Synology Drive connection target updated from:
192.168.10.187

to:
Existing QuickConnect ID

Final Synology Drive status:
- Windows desktop client: Online
- Pixel 9 Pro mobile client: Online

Both existing clients successfully reconnected after migration.
```

#### Evidence

- [22A-synology-drive-clients-online](<https://1drv.ms/i/c/54cda586f36f2027/IQBBt4zrs5cnTKDLs1YRO6ekAVWLl3aS27Ek6J4YesKYA2A?e=hIYDLd>)
- [22-synology-quickconnect-restored](<https://1drv.ms/i/c/54cda586f36f2027/IQCsglVKQcEiSoEP4HPD3PljAUU0YjVlGQ0nzh9QddyTv9A?e=JapBs8>)

### 23. Verify Synology Photos After Migration

Verified that Synology Photos remained functional after migrating the existing storage pool and DSM configuration from the DS218play to the DS920+.

Validation included confirming that the web interface could access the existing photo library and that the Synology Photos mobile client could connect to the migrated NAS.

#### Command

```text
N/A - verified through Synology Photos web and mobile applications.
```

#### Result

```text
Synology Photos operational.

Existing photo library accessible.
Pixel 9 Pro Synology Photos client connected successfully.
No additional application-level changes required.

Service functionality was restored by returning the DS920+ to
192.168.10.142.
```

## Findings

- The DS920+ recognized the transferred SHR storage pool, both 8 TB HDDs, the existing ext4 filesystem, shared folders, and the migrated DSM configuration.
- The storage migration itself completed successfully, but several DSM packages required repair afterward, including Active Insight, Secure SignIn Service, and Tailscale.
- Plex Media Server did not migrate cleanly on the NAS. Because Plex was already running successfully on the Dell OptiPlex 7050, the NAS package was removed rather than repaired.
- Synology Drive retained its registered clients, but those clients initially showed as Offline after migration. Updating the connection and restoring QuickConnect returned both the Windows desktop and Pixel 9 Pro clients to an Online state.
- SMB access and Synology Photos were successfully validated after migration.
- Tailscale reconnected to the existing tailnet after package repair, although the initial remote DSM test over cellular did not load and required separate follow-up.

## Outcome / Resolution

The DS218play to DS920+ HDD migration completed successfully. The existing SHR storage pool remained healthy, both 8 TB drives were recognized, the ext4 filesystem was preserved, and the existing data and shared folders remained accessible.

Post-migration package issues were reviewed and repaired where required. Plex remained hosted on the Dell OptiPlex 7050, SMB file sharing was verified, Synology Drive clients were restored through QuickConnect, and Synology Photos was confirmed operational.

The DS920+ is now the active NAS, with Bays 3 and 4 available for future storage expansion.

## Lessons Learned

- Verify storage-pool health, drive health, filesystem type, DSM version, shared folders, and installed packages before beginning a hardware migration.
- Preserve drive order during an HDD migration and validate the storage pool before making additional post-migration changes.
- A successful storage migration does not guarantee that every application or client will reconnect automatically; package and service validation must be performed separately.
- Network identity changes, including a new LAN IP address, can affect application connectivity even when the underlying data migration is successful.
- Documenting the pre-migration state makes it much easier to identify what changed and determine whether a post-migration issue is related to storage, packages, or network connectivity.

## General Evidence

Evidence is linked within the individual migration steps above.
