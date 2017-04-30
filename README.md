# calcardbackup

This Bash script exports calendars and addressbooks of given users from ownCloud/Nextcloud to .ics and .vcf files and saves them as a compressed file. Additional options are available.  

## Requirements

- local installation of ownCloud/Nextcloud >= 5.0 with MySQL/MariaDB or SQLite3 (PostgreSQL unsupported)
- Packages `grep`, `sed`, `curl` and according database packages (`mysql-server`/`mariadb-server` or `sqlite3`)

## Quick Installation Guide

1. Clone the repository to your server (outside of webroot!) and enter the repo:  
`git clone https://github.com/BernieO/calcardbackup`  
`cd calcardbackup`

2. Copy the example file to `users.txt`:  
`cp examples/users.txt.example users.txt`

3. In `users.txt`, insert ownCloud/Nextcloud usernames and, separated by a colon, corresponding passwords, one user per line, of all users to be backed up. Be aware that other people with access to the server might be able to read the stored passwords.

4. Change the ownership of repo to your webserver's user (here `www-data`) and restrict access to `users.txt`:  
`chown -R www-data:www-data .`  
`chmod 600 users.txt`

5. Run the script as user `www-data` and give as first argument the path to your ownCloud/Nextcloud instance (here `/usr/share/nginx/nextcloud`). If you are using a self-signed certificate, you might have to use option `-s` as well:  
`sudo -u www-data ./calcardbackup "/usr/share/nginx/nextcloud"`

6. Check output of script - it will tell, if it needs any other options.

7. Find your backup in directory `backups/`.

## Advanced

All options can be specified in a configuration file or as command line arguments. If started with no options at all or only `-b|--batch`, the script attempts to use files `calcardbackup.conf` and `users.txt` in the script's directory as configuration file and login information.
If no configuration file via option `-c|--config` is given, the path to your ownCloud/Nextcloud instance must be the very first argument. Find detailed description of all available options below.

## Options
```
Usage: ./calcardbackup [URL] [option [argument]] [option [argument]] [option [argument]] ...

Arguments in capital letters to options are mandatory.
Paths (FILE / DIRECTORY) are absolute paths or relative paths to working directory.

-a | --address URL
       Pass URL of Owncloud Installation to script.
       Only required for Owncloud < 7.0
-b | --batch
       Batch mode: print nothing to stdout, except for path to backup.
       Depending on configuration this will be:
         - absolute path of compressed backup file
       or if run with option -x|--uncompressed (see below)
         - absolute path of directory containing uncompressed files
-c | --configfile FILE
       Read configuration from FILE. See 'examples/calcardbackup.conf.example'
       All other options except for -b|--batch will be ignored!
-d | --date FORMAT
       Use FORMAT as file name extension for backup directory or compressed backup file.
       FORMAT needs to be a format descriptor of the command date().
       The default is -%Y-%m-%d and will result in a directory or file
       named: 'calcardbackup-2017-03-23' or 'calcardbackup-2017-03-23.tar.gz'
       Check "man date" for more info about different formats and syntax.
-e | --encrypt FILE
       Encrypt backup file with AES256 (gnupg). First line of FILE will be used as passphrase
-o | --output DIRECTORY
       Use directory DIRECTORY to store backups.
       If not given, folder 'backups' in script's directory is created and used.
-r | --remove N
       Remove backups older than N days from backup folder (N needs to be a positive integer).
-s | --selfsigned
       Needs to be given if certificate is selfsigned or for any other reason not trustful to curl.
-u | --usersfile FILE
       Give location of FILE, which contains users to be backed up. One user per line, with corresponding
       password separated by a colon. See 'examples/users.txt.example'
       If this option is not given, calcardbackup will search for a file named
       'users.txt' in script's directory.
-x | --uncompressed
       don't compress backup folder
-z | --zip
       use zip to compress backup folder instead of creating a gzipped tarball (tar.gz)
```

## Considerations about encryption
If you want to use the included encryption possibility, be aware that:
- the files are encrypted by Gnupg, AES256 with the passphrase given in a separate file
- the passphrase is stored in a file. Other users with access to the server might be able to see the passphrase.
- `calcardbackup` is designed to run without user interaction, so there can't be a rock solid encryption. I consider the offered one as sufficient in most cases though.
- if you need rock solid encryption, don't let `calcardbackup` encrypt the backup. Instead, encrypt it yourself.

## Want to read some of that in german?

There is a little article about this also in german available:  
[Blog article about calcardbackup](https://bob.gatsmas.de/articles/calcardbackup-kalender-und-adressbuchbackup-von-owncloud-nextcloud)
