# Disaster Recovery on AWS: Restore an EBS Volume from a Snapshot

## Problem
A server's data disk was deleted. The business data on it must be recovered quickly.

## Solution
Take a snapshot (a backup) of the EBS volume. If the volume is lost, create a new volume from the snapshot and attach it to the server.

## Steps
1. Launched an Amazon Linux EC2 instance and attached a 2 GB EBS volume in the same Availability Zone
2. Formatted it with XFS and mounted it at `/data`
3. Created sample data (`customers.txt`, `invoice.txt`)
4. Created a snapshot named `data-backup-1` and waited until it was Completed
5. Simulated the disaster: unmounted, detached and deleted the volume
6. Created a new volume from the snapshot in the same Availability Zone
7. Attached it to the instance and mounted it at `/data`
8. Verified that all files were restored

## Commands used
```bash
lsblk                                  # list disks
sudo mkfs -t xfs /dev/nvme1n1          # format the new disk (only for the first, empty disk)
sudo mkdir /data
sudo mount /dev/nvme1n1 /data          # connect the disk to the folder
sudo umount /data                      # disconnect before detaching
```
The restored disk was **not** formatted again, because formatting would erase the data.

## Screenshots

Before the disaster:

<img width="1365" height="767" alt="Screenshot 2026-10-29 195711" src="https://github.com/user-attachments/assets/ff24b460-98b5-4bb0-bc1b-99197ca9252b" />


Snapshot completed:

<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/f8978458-3255-4cd5-9daa-071500330df3" />


Volume deleted (the incident):

<img width="1365" height="767" alt="Screenshot 2026-10-29 195932" src="https://github.com/user-attachments/assets/fcd1b846-6ee5-436a-bb3b-617753a81ba7" />


After the restore:

<img width="1365" height="767" alt="Screenshot 2026-10-29 200243" src="https://github.com/user-attachments/assets/7f236516-098c-404c-b941-06b53cc57d62" />


## Recovery runbook
1. Detect that the data volume is missing
2. Find the latest snapshot in EC2 → Snapshots
3. Create a volume from it in the same Availability Zone as the server
4. Attach the volume to the instance
5. Mount it and check the files (do not run `mkfs`)

## What I learned
- What an EBS snapshot is and how to restore from one
- A volume can only attach to a server in the same Availability Zone
- Never format a restored disk
- How to write a simple recovery runbook

## Tools
AWS (EC2, EBS, Snapshots), Linux
