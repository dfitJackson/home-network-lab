# Restore OptiPlex NFS Media Mounts After Synology Migration

- **Category:** Services
- **Status:** Resolved
- **Created:** 2026-07-27
- **Last updated:** 2026-07-27

## Activities

- Configuration
- Troubleshooting

## Tags

- Containers
- Docker
- NFS
- Plex
- Synology

## Objective / Problem

After migrating storage from the Synology DS218play to the DS920+, Plex Media Server remained hosted on the Dell OptiPlex 7050. Plex accesses its media through NFS, and because the OptiPlex NFS mounts continued to reference the former NAS IP address, connectivity was disrupted. This task documents restoring NFS service and permissions on the DS920+, validating the existing Ubuntu NFS mounts, and confirming Plex media access.

## Environment Components

- Plex
- Synology
- Ubuntu Server

## Environment Notes

None recorded.

## Tools

- Docker Compose

## Steps

### 1. Identify Stale NFS Mount Configuration

Checked the OptiPlex to determine how Synology storage was presented to Plex. The investigation confirmed that the server uses NFS and that both the active mounts and `/etc/fstab` still referenced the former DS218play IP address.

#### Result

```text
Existing NFS mounts:

192.168.10.142:/volume1/Media
→ /mnt/media

192.168.10.142:/volume1/backups
→ /mnt/backups

Old NAS IP:
192.168.10.142

New DS920+ IP:
192.168.10.187

/etc/fstab still references the former NAS.

Additional configuration issue identified:
The backups entry contains "nfvers=3" rather than "nfsvers=3".
```

#### Evidence

- [01-stale-nfs-mounts-old-nas-ip](<https://1drv.ms/i/c/54cda586f36f2027/IQBa3dNPskh0ToWNtd3gSb85AQkldDRVdpdGKNAAmxUKXzs?e=D8GmSp>)

### 2. Verify DS920+ NFS Configuration

Reviewed the migrated NFS configuration on the DS920+. Existing NFS permissions were found referencing the OptiPlex server at 192.168.10.193, indicating that at least part of the previous NFS configuration was preserved during the HDD migration.

#### Result

```text
Audiobooks NFS rule retained.

Authorized client:
192.168.10.193

Privilege: Read/Write
Squash: No mapping
Security: sys
Asynchronous: Enabled
Non-privileged ports: Allowed
Mounted subfolder access: Allowed

Mount path:
/volume1/Audiobooks
```

#### Evidence

- [02-validate-nfs-config-synology](<https://1drv.ms/i/c/54cda586f36f2027/IQCMABgaKSyzRp3wIwdoFXnKAbKI2xBzCiun52PCCGl_VhM?e=GGXd4a>)

### 3. Restore Previous NAS IP Using DHCP Reservation

The DS920+ initially received `192.168.10.187` from the BE550 DHCP server. Existing NFS mounts and other dependencies referenced the former NAS at `192.168.10.142`. A DHCP reservation was created on the BE550 to assign the previous NAS address to the DS920+.

#### Result

```text
DHCP server: TP-Link BE550

Device:
DJServer

Previous dynamic IP:
192.168.10.187

Reserved IP:
192.168.10.142

Reservation status:
Enabled
```

#### Evidence

- [03-synology-ip-reservation-tplink](<https://1drv.ms/i/c/54cda586f36f2027/IQAXF98c0E9CRaMuoUtVb9brAT_cFy7tRDenO5lf3xeiEMc?e=GvmDno>)
- [03A-synology-reboot-ip-configured](<https://1drv.ms/i/c/54cda586f36f2027/IQCvLDip4P_zQJe9EUz6mER4AYGB_IuNQWvttAWeicgw-8s?e=r2YTFu>)

### 4. Verify NFS and Restore Plex Media Access

After assigning the DS920+ the former NAS IP address through a BE550 DHCP reservation, the OptiPlex was able to reconnect to its existing NFS media mounts. No Plex library-path changes were required.

#### Command

```text
showmount -e 192.168.10.142

timeout 10 ls -lah /mnt/media | head -20

findmnt /mnt/media
```

#### Result

```text
dacksonj@dackson-OptiPlex-7050:~$ showmount -e 192.168.10.142
Export list for 192.168.10.142:
/volume1/Media      192.168.10.193
/volume1/TV Shows   192.168.10.193
/volume1/Movies     192.168.10.193
/volume1/backups    192.168.10.193
/volume1/Audiobooks 192.168.10.193

dacksonj@dackson-OptiPlex-7050:~$ timeout 10 ls -lah /mnt/media | head -20
total 88K
drwxrwxrwx  14 root root  4.0K Jul  8 15:53 .
drwxr-xr-x   4 root root  4.0K Feb 13 13:33 ..
drwxrwxrwx  91 1026 users 4.0K Jul 15 14:33 Audiobooks
drwxr-xr-x   2 1024 users 4.0K Mar  4 15:25 audiobooks
drwxrwxrwx  12 1024 users 4.0K Jul 16 13:07 downloads
drwxrwxrwx   3 1026 users 4.0K Feb 16 12:36 Games
drwxr-xr-x   2 1024 users 4.0K Jul 23 18:20 incomplete
drwxrwxrwx  28 1026 users 4.0K Jul  4 16:26 Kids Movies
drwxrwxrwx   8 1026 users 4.0K Apr 15 07:47 Kids TV Shows
drwxrwxrwx 164 1026 users  20K Jul 23 18:21 Movies
drwxrwxrwx   5 1024 users 4.0K Jul 16 12:52 music
drwxrwxrwx   3 root root  4.0K Feb 16 22:41 #recycle
drwxrwxrwx  89 1026 users  20K Jul  5 18:04 TV Shows

dacksonj@dackson-OptiPlex-7050:~$ timeout 10 ls -lah /mnt/backups | head -20
ls: cannot open directory '/mnt/backups': Permission denied
dacksonj@dackson-OptiPlex-7050:~$ timeout 10 ls -lah /mnt/backups | head -20
total 24K
drwxrwxrwx 6 root root  4.0K Jun 10 12:57 .
drwxr-xr-x 4 root root  4.0K Feb 13 13:33 ..
drwxrwxrwx 2 root root  4.0K Jul 24 15:27 audiobookshelf
drwxrwxrwx 5 1026 users 4.0K Jun 12 10:07 DSurface Preset-10-06-26
drwxrwxrwx 3 root root  4.0K Jun 10 14:06 #recycle
```

### 5. Correct Backup Share NFS User Mapping

After restoring the DS920+ to the previous NAS IP address, the Media NFS share recovered successfully and Plex resumed operation.

The backups NFS mount was present but returned `Permission denied` when accessed from the OptiPlex.

Comparing the NFS permissions between the working Media share and the backups share identified a difference in the Squash setting.

Media was configured as `Map all users to admin`, while backups used `No mapping`.

The backups NFS permission was updated to use `Map all users to admin`.

#### Command

```text
timeout 10 ls -lah /mnt/backups | head -20
```

#### Result

```text
Before fix:
ls: cannot open directory '/mnt/backups': Permission denied

After fix (adding `Map all users to admin` for the backup folder):
total 24K
drwxrwxrwx 6 root root  4.0K Jun 10 12:57 .
drwxr-xr-x 4 root root  4.0K Feb 13 13:33 ..
drwxrwxrwx 2 root root  4.0K Jul 24 15:27 audiobookshelf
drwxrwxrwx 5 1026 users 4.0K Jun 12 10:07 DSurface Preset-10-06-26
drwxrwxrwx 3 root root  4.0K Jun 10 14:06 #recycle

Backup NFS access restored successfully.

Root cause:
NFS Squash/user mapping mismatch.

Previous:
No mapping

Corrected:
Map all users to admin
```

### 6. Verify Backup NFS Write Access

Performed a temporary file creation and deletion test on the restored `/mnt/backups` NFS mount to confirm that the OptiPlex had functional read/write access after correcting the Synology NFS user mapping.

#### Command

```text
touch /mnt/backups/.nfs-write-test &&
ls -l /mnt/backups/.nfs-write-test &&
rm /mnt/backups/.nfs-write-test &&
echo "NFS backup write test passed"
```

#### Result

```text
-rwxrwxrwx 1 1024 users 0 Jul 27 13:25 /mnt/backups/.nfs-write-test
NFS backup write test passed

Read/write access to the backups NFS share was successfully verified.

The OptiPlex created, listed, and removed a temporary test file on
/mnt/backups without error.

NFS backup access is fully operational.
```

## Findings

The primary issue following the Synology migration was that the new DS920+ received a different IP address from the original NAS. Several services and mounts on the OptiPlex were still configured to reference the previous Synology IP address, which caused services such as Plex and NFS-mounted storage to lose access to their dependencies.

The initial remediation considered was to update each affected service individually, including Docker Compose configurations and other application settings, so they would point to the new NAS IP address.

During troubleshooting, it became clear that the IP address itself was the shared dependency across multiple services rather than each service having an independent problem.

## Outcome / Resolution

Rather than reconfiguring each dependent service individually, the DS920+ was assigned the previous NAS IP address through a DHCP reservation on the TP-Link BE550, which is currently providing DHCP services for the network.

This restored the original network endpoint used by the OptiPlex and other services. As a result, multiple services recovered without requiring separate application-level configuration changes.

Plex media access was restored through the existing NFS mount configuration. Synology Photos and other services that depended on the previous NAS address also regained connectivity.

A separate NFS permission issue was identified on the backups share and corrected by aligning its Squash configuration with the working Media share. Read and write access to the backup share was then successfully verified.

**Final status: Resolved.**

## Lessons Learned

This troubleshooting process reinforced the importance of identifying the common dependency behind multiple service failures before modifying individual applications.

My initial understanding was that I could resolve the problem by updating every service and configuration that referenced the old NAS IP address. While technically valid, this would have required multiple configuration changes, increased troubleshooting time, and introduced additional opportunities to break services that were otherwise functioning correctly.

Instead, I recognized that all of these services shared the same dependency: the network address of the NAS. Restoring that single dependency through a DHCP reservation provided a simpler and more centralized solution.

This also demonstrated the value of maintaining predictable IP addressing for infrastructure devices. Services such as NAS systems, servers, switches, and other infrastructure components should ideally have persistent addresses through DHCP reservations or another controlled addressing strategy.

The key lesson was:

> When multiple services fail after an infrastructure change, look for the shared dependency before modifying each service individually.

Using a centralized fix reduced the number of configuration changes, preserved the existing service architecture, and created a simpler point of reference for future troubleshooting.

## General Evidence

None recorded.
