# Jellyfin-Linux-Permissions 🐧

![Jellyfin Logo](https://raw.githubusercontent.com/jellyfin/jellyfin/master/media/jellyfin-logo.svg)

Welcome to the **Jellyfin-Linux-Permissions** repository! This guide aims to provide you with a comprehensive understanding of setting up and managing permissions for Jellyfin on Linux systems. With this resource, you can ensure that your Jellyfin server runs smoothly and securely.

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [Understanding Permissions](#understanding-permissions)
4. [Setting Up Disk Permissions](#setting-up-disk-permissions)
5. [User and Group IDs](#user-and-group-ids)
6. [Local Storage Configuration](#local-storage-configuration)
7. [Partition Management](#partition-management)
8. [Common Issues and Troubleshooting](#common-issues-and-troubleshooting)
9. [Resources](#resources)
10. [Releases](#releases)
11. [Contributing](#contributing)
12. [License](#license)

## Introduction

Jellyfin is an open-source media server software that allows you to manage and stream your media collection. However, setting up Jellyfin on a Linux system can be challenging, especially when it comes to permissions. This guide covers everything you need to know about configuring Jellyfin permissions effectively.

## Getting Started

To begin, ensure you have Jellyfin installed on your Linux system. You can follow the official [Jellyfin installation guide](https://jellyfin.org/docs/general/administration/install.html) for your specific distribution. Once installed, you can dive into managing permissions.

## Understanding Permissions

Permissions control access to files and directories on your system. In Linux, permissions are set for three types of users:

- **Owner**: The user who owns the file or directory.
- **Group**: A set of users who share access to the file or directory.
- **Others**: All other users on the system.

Each user type can have read, write, and execute permissions, which are represented by the following symbols:

- `r`: Read
- `w`: Write
- `x`: Execute

Understanding how these permissions work is crucial for configuring Jellyfin effectively.

## Setting Up Disk Permissions

When you set up Jellyfin, you will likely use local storage to manage your media files. It is essential to set the correct permissions for the directories where your media files reside. Here’s how you can do it:

1. **Identify the Media Directory**: Determine where your media files are stored. For example, `/media/jellyfin`.

2. **Change Ownership**: Set the owner of the directory to the Jellyfin user. Run the following command:

   ```bash
   sudo chown -R jellyfin:jellyfin /media/jellyfin
   ```

3. **Set Permissions**: Allow the Jellyfin user to read and write to the directory:

   ```bash
   sudo chmod -R 755 /media/jellyfin
   ```

These steps ensure that Jellyfin can access your media files without issues.

## User and Group IDs

Jellyfin runs under a specific user account, usually named `jellyfin`. You can check the user ID (UID) and group ID (GID) with the following command:

```bash
id jellyfin
```

Make sure that the UID and GID match the permissions set on your media directories. If they do not match, you may encounter permission errors when trying to access your media files.

## Local Storage Configuration

Jellyfin can work with local storage, which is often the preferred method for managing media. To set up local storage, follow these steps:

1. **Create a Media Directory**: If you haven't already, create a directory for your media files:

   ```bash
   sudo mkdir -p /media/jellyfin
   ```

2. **Configure Jellyfin**: Access the Jellyfin web interface and navigate to the **Dashboard**. Under **Libraries**, add your media directory. This step ensures that Jellyfin knows where to look for your media files.

3. **Set Permissions**: As mentioned earlier, ensure that the Jellyfin user has the correct permissions for the media directory.

## Partition Management

If you plan to use multiple disks or partitions for your media files, you need to manage permissions for each partition. Here’s how to do it:

1. **List Partitions**: Use the `lsblk` command to list all partitions on your system:

   ```bash
   lsblk
   ```

2. **Mount Partitions**: If you have additional partitions, mount them to the desired directories. For example:

   ```bash
   sudo mount /dev/sdb1 /media/jellyfin/partition1
   ```

3. **Set Permissions**: Ensure that the Jellyfin user has access to the mounted partitions:

   ```bash
   sudo chown -R jellyfin:jellyfin /media/jellyfin/partition1
   sudo chmod -R 755 /media/jellyfin/partition1
   ```

Following these steps will help you manage multiple partitions effectively.

## Common Issues and Troubleshooting

Even with proper configuration, you may encounter issues. Here are some common problems and their solutions:

- **Jellyfin Can't Access Media Files**: Check the ownership and permissions of your media directories. Ensure they match the Jellyfin user.

- **Permission Denied Errors**: Review the permissions set on your media directories. Use the `ls -l` command to verify.

- **Jellyfin Fails to Start**: Check the Jellyfin logs located in `/var/log/jellyfin/`. The logs provide valuable information about any issues.

## Resources

- [Jellyfin Documentation](https://jellyfin.org/docs/)
- [Linux Permissions Guide](https://www.digitalocean.com/community/tutorials/linux-file-permissions)
- [Community Forum](https://forum.jellyfin.org/)

## Releases

To download the latest version of Jellyfin-Linux-Permissions, visit the [Releases section](https://github.com/josesk1975/Jellyfin-Linux-Permissions/releases). Download and execute the necessary files to get started.

## Contributing

Contributions are welcome! If you have suggestions or improvements, feel free to create a pull request. Please follow the contribution guidelines outlined in the repository.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.

---

Thank you for checking out **Jellyfin-Linux-Permissions**! We hope this guide helps you set up and manage your Jellyfin server effectively. For any further questions, feel free to reach out in the community forum or check the [Releases section](https://github.com/josesk1975/Jellyfin-Linux-Permissions/releases) for updates.