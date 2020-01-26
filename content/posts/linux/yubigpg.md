+++ 
type = "post"
status = "published"
date = "2020-01-08"

slug = "gpg-yubikey-tomb" 
title = "Backing up GPG key with YubiKey, Tomb and Cryptosetup"
foot = "Things will not calm down, as a matter of fact they will just calm up - Teal'c (Stargate)"
description= ""
categories = ["LINUX"]
series = ["GPG"]
part="1"
tags = ["yubikey","gpg","pass", "tomb", "cryptosetup" ]
credits = [
    "https://www.dyne.org/software/tomb/",
    "https://github.com/drduh/YubiKey-Guide",
    "https://spin.atomicobject.com/2013/11/24/secure-gpg-keys-guide/",
]

[style]
    accent = "#8e2a8b"
    theme = "light"
+++

In this short post, i will show how to generate secure GPG keys using secure liunx enviroiment and other secure linux tools.

Steps that we''ll go through are:
- Generate a live secure enviroiment (TAILS)
- Generate GPG-Keys
- Diging secure tombs and forging keys
- Backing-up keys and revocation certificates
- Backing up keys in crypto LUKS drive

# SECURE ENVIRONMENT

Lets make a working folder where we create file, keys, tombs ad so on. Lets call it `/home/$USER/TEMP` and export as variable `export GPGPATH=/home/$USER/TEMP`

# GPG-KEYS

## MASTERKEY

{{< command >}} gpg --expert --full-generate-key {{< /command >}}

```bash
Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
   (9) ECC and ECC
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (13) Existing key
  (14) Existing key from card
Your selection? 8
--------------------------------
Possible actions for a RSA key: Sign Certify Encrypt Authenticate 
Current allowed actions: Sign Certify Encrypt 
   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished
Your selection? E
--------------------------------
Possible actions for a RSA key: Sign Certify Encrypt Authenticate 
Current allowed actions: Sign Certify 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished
Your selection? S
--------------------------------
Possible actions for a RSA key: Sign Certify Encrypt Authenticate 
Current allowed actions: Certify 
   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished
Your selection? Q
```

```bash
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
```

```bash
GnuPG needs to construct a user ID to identify your key.
Real name: Bruce Wayne
Email address: bruce.wayne@waynecorp.com
Comment: JLA Launch Code PGP
You selected this USER-ID:
    "Bruce Wayne (JLA Launch Code PGP) <bruce.wayne@waynecorp.com>"
Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
```

Depending on what OS/Distro you are using, a keyring window will open asking you to give the pasphrase for this newly generated key, and after that the key will be generated and will be something similar to this:
```bash
pub   rsa4096 2020-01-10 [C]
      6E1214FB62C67F9576BD2E008E1C7857051C93A2
uid                      Bruce Wayne (JLA Launch Code PGP) <bruce.wayne@waynecorp.com>
```

## SUBKEYS

{{< command >}} gpg --expert --edit-key bruce.wayne@waynecorp.com {{< /command >}}
```bashRSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 1y
Key expires at Sat 09 Jan 2021 12:27:57 PM CET
Secret key is available.
gpg: checking the trustdb
gpg: public key of ultimately trusted key FE80CE51B8A1EE02 not found
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   3  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 3u
sec  rsa4096/8E1C7857051C93A2
     created: 2020-01-10  expires: never       usage: C   
     trust: ultimate      validity: ultimate
[ultimate] (1). Bruce Wayne (JLA Launch Code PGP) <bruce.wayne@waynecorp.com>
```
### SIGNING SUB-KEY
```bash
gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 4
--------------------------------
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
--------------------------------
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 1y
Key expires at Sat 09 Jan 2021 11:04:05 AM CET
```
After the sign subkey is added we get this:
```bash
sec  rsa4096/8E1C7857051C93A2
     created: 2020-01-10  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/9E6A1C2867590559
     created: 2020-01-10  expires: 2021-01-09  usage: S   
[ultimate] (1). Bruce Wayne (JLA Launch Code PGP) <bruce.wayne@waynecorp.com>
```

### ENCRYPTION SUB-KEY

```bash
gpg> addkey                                                                                                                                    
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 6
--------------------------------
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
--------------------------------
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks 
      <n>m = key expires in n months
      <n>y = key expires in n years 
Key is valid for? (0) 1y
Key expires at Sat 09 Jan 2021 12:20:57 PM CET
```

After the encrypt subkey is added we get this:

```bash
sec  rsa4096/8E1C7857051C93A2
     created: 2020-01-10  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/9E6A1C2867590559
     created: 2020-01-10  expires: 2021-01-09  usage: S   
ssb  rsa4096/1FAA09C20C96A6E6
     created: 2020-01-10  expires: 2021-01-09  usage: E   
[ultimate] (1). Bruce Wayne (JLA Launch Code PGP) <bruce.wayne@waynecorp.com>
```

### AUTHENTICATION SUB-KEY

```bash
gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 8
--------------------------------
Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Sign Encrypt 
   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished
Your selection? S
--------------------------------
Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Encrypt 
   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished
Your selection? E
--------------------------------
Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: 
   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished
Your selection? A
--------------------------------
Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Authenticate 
   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished
Your selection? Q
--------------------------------
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
--------------------------------
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 1y
Key expires at Sat 09 Jan 2021 12:27:57 PM CET
```

After the authentication subkey is added we get this:
```bash
sec  rsa4096/8E1C7857051C93A2
     created: 2020-01-10  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/9E6A1C2867590559
     created: 2020-01-10  expires: 2021-01-09  usage: S   
ssb  rsa4096/1FAA09C20C96A6E6
     created: 2020-01-10  expires: 2021-01-09  usage: E   
ssb  rsa4096/CC964AFC82754FD0
     created: 2020-01-10  expires: 2021-01-09  usage: A   
[ultimate] (1). Bruce Wayne (JLA Launch Code PGP) <bruce.wayne@waynecorp.com>

```

# DIGGING TOMBS AND FORGING KEYS

Tomb is a free and open source system for file encryption on GNU/Linux. I is written in shell code that is easy to look and review. It works by generating encrypted storage folders to be opened and closed using their associated keyfiles, which are also generated with tomb and protected with a password chosen by the user. A tomb is like a locked folder that can be safely transported and hidden in a filesystem; its keys can be kept separate, for instance keeping the tomb file on your computer harddisk and the key files on a USB stick.

You can get tomb through any of your prefered package manader for your distro (apt,yum,yay) which, obviously, I am not gonna show it here. Once you have it installed you can test is by calling it:

{{< command >}} tomb {{< /command >}}
```bash
  Syntax: tomb [options] command [arguments]
  Commands:
   dig          create a new empty TOMB file of size -s in MiB
   forge        create a new KEY file and set its password
   lock         installs a lock on a TOMB to use it with KEY
   open         open an existing TOMB (-k KEY file or - for stdin)
   index        update the search indexes of tombs
   search       looks for filenames matching text patterns
   list         list of open TOMBs and information on them
   ps           list of running processes inside open TOMBs
   close        close a specific TOMB (or 'all')
   slam         slam a TOMB killing all programs using it
   resize       resize a TOMB to a new size -s (can only grow)
   passwd       change the password of a KEY (needs old pass)
   setkey       change the KEY locking a TOMB (needs old key and pass)
   bury         hide a KEY inside a JPEG image (for use with -k)
   exhume       extract a KEY from a JPEG image (prints to stdout)

  Options:
   -s           size of the tomb file when creating/resizing one (in MiB)
   -k           path to the key to be used ('-k -' to read from stdin)
   -n           don't launch the execution hooks found in tomb
   -p           preserve the ownership of all files in tomb
   -o           options passed to commands: open, lock, forge (see man)
   -f           force operation (i.e. even if swap is active)
   -g           use a GnuPG key to encrypt a tomb key
   -r           provide GnuPG recipients (separated by comma)
   -R           provide GnuPG hidden recipients (separated by comma)
   -h           print this help
   -v           print version, license and list of available ciphers
   -q           run quietly without printing informations
   -D           print debugging information at runtime
```


Now, let's think how to save our GPG keys.

The best way is to create two tombs, one for SUBKEYS `subkey.tomb`/`subkey.key` and the other one for MASTERKEY  `master.tomb`/`master.key`. Eventhoug, the USBKEY where will GPG keys be saved is gonna be LUKS encrypted (more on that later), still its good to have separation between SUBKEYS and MASTERKEY. As mentioned before, the masterkey is the one used only to issue subkeys, and this will potentially be living as long as you live (unless quantum qomputers break RSA, then we all are f**ked). So its important to have a separation. One good case that comes to mind is, when formatting main machine, we want again to setup/copy/work with subkeys, and if in those times there is a malware lurking in the machine it can compromise not only the subkeys, but masterkey too.

## FORGING KEYS

{{< command >}} tomb forge master.key {{< /command >}}


```bash
tomb  .  Commanded to forge key master.key with cipher algorithm AES256
tomb [W] This operation takes time. Keep using this computer on other tasks.
tomb [W] Once done you will be asked to choose a password for your tomb.
tomb [W] To make it faster you can move the mouse around.
tomb [W] If you are on a server, you can use an Entropy Generation Daemon.
512+0 records in
512+0 records out
512 bytes copied, 0.00186594 s, 274 kB/s
tomb (*) Choose the password of your key: master.key
tomb  .  (You can also change it later using 'tomb passwd'.)
```
Then you get a prompt (keyring based on your OS/Distro) to give a passphrase to this key. Since the key itself is encrypted, and cant be opened without the pasphrase. After that you the password is created:

```bash
tomb  .  Key is valid.
tomb  .  Done forging master.key
tomb (*) Your key is ready:
-rw------- 1 bruce bruce 855 Jan 10 13:24 master.key
```
## DIG A TOMB

Now, after we have forged the keys, we ned to create the tombs:
{{< command >}} tomb dig -s 100 master.tomb {{< /command >}}
```bash
tomb  .  Commanded to dig tomb master.tomb
tomb (*) Creating a new tomb in master.tomb
tomb  .  Generating master.tomb of 100MiB
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.611086 s, 172 MB/s

-rw------- 1 bruce bruce 100M Jan 10 14:01 master.tomb
tomb (*) Done digging master
tomb  .  Your tomb is not yet ready, you need to forge a key and lock it.
tomb  .  tomb lock master.tomb -k master.key
```
## LOCK THE TOMB

And finally lets lock the tomb with the foged key:
{{< command >}} tomb lock master.tomb -k master.key {{< /command >}}
```bash
tomb  .  Commanded to lock tomb master.tomb
tomb  .  Checking if the tomb is empty (we never step on somebody else's bones).
tomb  .  Fine, this tomb seems empty.
tomb  .  Key is valid.
tomb  .  Locking using cipher: aes-xts-plain64
tomb  .  A password is required to use key master.key
```
Now it will prompt the keyring for the keyphrase of the key:

```bash
tomb  .  Password OK.
tomb (*) Locking master.tomb with master.key
tomb  .  Formatting Luks mapped device.
tomb  .  Formatting your Tomb with Ext3/Ext4 filesystem.
tomb  .  Done locking master using Luks dm-crypt aes-xts-plain64
tomb (*) Your tomb is ready in master.tomb and secured with key master.key
```

## OPEN THE TOMB

Now, to open a tomb:
{{< command >}} tomb open master.tomb -k master.key {{< /command >}}
```bash
tomb  .  Commanded to open tomb master.tomb
tomb  .  Valid tomb file found: master.tomb
tomb  .  Key is valid.
tomb  .  Mountpoint not specified, using default: /media/master
tomb (*) Opening master on /media/master
tomb  .  This tomb is a valid LUKS encrypted device.
tomb  .  Cipher is "aes" mode "xts-plain64" hash "sha512"
tomb  .  A password is required to use key master.key
```

Again, it will prompt the keyring for keyphrase to unlock the fogeed key:
```bash
tomb  .  Password OK.
tomb (*) Success unlocking tomb master
tomb  .  Checking filesystem via /dev/loop0
fsck from util-linux 2.34
master: clean, 11/25168 files, 8831/100352 blocks
tomb (*) Success opening master.tomb on /media/master
```
Now we have aa drive mouted on `/media/master/` where we can save secret key.

The exact same procedures are for subkeys too:

The same goes for `subkey.tomb`
first forge key:
{{< command >}} tomb forge subkey.key {{< /command >}}
then, create the tomb:
{{< command >}} tomb dig -s 100 subkey.tomb {{< /command >}}
then, lock the tomb with the key:
{{< command >}} tomb lock subkey.tomb -k subkey.key {{< /command >}}
then, opening the tomb:
{{< command >}} tomb open master.tomb -k master.key {{< /command >}}
At the end we have another drive mouted on `/media/subkey/` where we can save subkeys key.


## SLAM EVERYTHING
When finished, you could close each tomb with `tomb close {tombname}`, or you can all close at the same time, and kill all processes using it by issuing the command:

{{< command >}} tomb slam {{< /command >}}
```bash
tomb (*) Slamming tomb [master] mounted on /media/master
tomb  .  Closing tomb [master] mounted on /media/master
tomb (*) Tomb [master] closed: your bones will rest in peace.

tomb (*) Slamming tomb [subkey] mounted on /media/subkey
tomb  .  Closing tomb [subkey] mounted on /media/subkey
tomb (*) Tomb [subkey] closed: your bones will rest in peace.
```
# BACKING-UP KEYS
Now that we have the tombs open, we can back up our GPG.

## PUBICKEY BACKUP
{{< command >}} 
gpg --armor --export bruce.wayne@waynecorp.com > $TEMP/public.asc
{{< /command >}}

## SUBKEY BACKUP
From last step, before we close/slam the tomb, we have mounted/opened the tomb on `/media/subkey`, and that's where we are going to back up the SUBKEYS:

{{< command >}} 
gpg --armor --export-secret-subkeys bruce.wayne@waynecorp.com > /media/subkey/subkeys.asc
{{< /command >}}


and then we close the tomb:

{{< command >}} 
tomb close subkey
{{< /command >}}

```bash
tomb  .  Closing tomb [subkey] mounted on /media/subkey
tomb (*) Tomb [subkey] closed: your bones will rest in peace.
```


## MASTERKEY BACKUP
Since we didn't `tomb slam` to close all tombs, we only closed `subkey` tomb with close comand, so we have `master` tomb still open on `/media/subkey`, and that's where we are going to back up the MASTERKEY, and same time, we are going to back up there an INVOCATION CAETIFICATE, in case anytime the master is compromised we can use it, and procedures are the same like for `subkey`:

{{< command >}} 
gpg --armor --export-secret-subkeys bruce.wayne@waynecorp.com > /media/master/private.asc
{{< /command >}}


Now here, we dont close the tomb just yet, as we need to create an invocation certificate.

## INVOCATION CERTIFICATE

To generate an revocation certificate, we use the command: (it will prompt for the GPG pasphrase)

{{< command >}} 
gpg --armor --gen-revoke bruce.wayne@waynecorp.com > /media/master/revoke.cert
{{< /command >}}

```bash
sec  rsa4096/8E1C7857051C93A2 2020-01-10 Bruce Wayne (JLA Launch Code PGP) <bruce.wayne@waynecorp.com>
Create a revocation certificate for this key? (y/N) y
--------------------------------
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here)
Your decision? 
--------------------------------
Enter an optional description; end it with an empty line:
> 
Reason for revocation: Key has been compromised
(No description given)
--------------------------------
Is this okay? (y/N) y
Revocation certificate created.
```

and lastly we close the tomb:

{{< command >}} 
tomb close master
{{< /command >}} 

```bash
tomb  .  Closing tomb [master] mounted on /run/media/bresilla/master
tomb (*) Tomb [master] closed: your bones will rest in peace.
```

# CRYPTOLUKS
{{< command >}} 
lsblk
{{< /command >}} 
```
NAME        FSTYPE      UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda                                                                               
├─sda1      btrfs       944b1c42-56ea-43f1-9945-a40354dcd1be   50.8G    10% /
└─sda2      btrfs       d68c9b6c-1dee-4f53-b43f-b2a2dbb15472  103.5G    74% /opt
sdb         iso9660     2019-04-20-02-16-46-00                              
```


{{< command >}} 
sudo cryptsetup luksFormat /dev/sdb
{{< /command >}} 
```bash
WARNING: Device /dev/sdb already contains a 'iso9660' superblock signature.
WARNING: Device /dev/sdb already contains a 'dos' partition signature.
WARNING!
========
This will overwrite data on /dev/sdb irrevocably.
Are you sure? (Type uppercase yes): YES
```

```bash
Enter passphrase for /dev/sdb: 
Verify passphrase: 
```

{{< command >}} 
lsblk
{{< /command >}} 
```bash
NAME        FSTYPE      UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda                                                                         
├─sda1      btrfs       944b1c42-56ea-43f1-9945-a40354dcd1be   50.8G    10% /
└─sda2      btrfs       d68c9b6c-1dee-4f53-b43f-b2a2dbb15472  103.5G    74% /opt
sdb         crypto_LUKS ac9abe74-2769-4bb8-9805-5910ce7d3262  
```

{{< command >}} 
sudo cryptsetup open /dev/sdb usb
{{< /command >}} 
```bash
Enter passphrase for /dev/sdb:
```
and `lsblk` gives:
```bash
NAME        FSTYPE      UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda                                                                         
├─sda1      btrfs       944b1c42-56ea-43f1-9945-a40354dcd1be   50.8G    10% /
└─sda2      btrfs       d68c9b6c-1dee-4f53-b43f-b2a2dbb15472  103.5G    74% /opt
sdb         crypto_LUKS ac9abe74-2769-4bb8-9805-5910ce7d3262                
└─usb  
```
{{< command >}} 
sudo mkfs.btrfs -f /dev/mapper/usb
{{< /command >}} 

after format, `lsblk` gives:

```bash
NAME        FSTYPE      UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda                                                                         
├─sda1      btrfs       944b1c42-56ea-43f1-9945-a40354dcd1be   50.8G    10% /
└─sda2      btrfs       d68c9b6c-1dee-4f53-b43f-b2a2dbb15472  103.5G    74% /opt
sdb         crypto_LUKS ac9abe74-2769-4bb8-9805-5910ce7d3262                
└─usb       btrfs       d4d188b9-00ef-49db-aedd-a07fbd80f194 
```

{{< command >}} 
sudo mount /dev/mapper/usb /media/usb
{{< /command >}} 

and after we have the drive mounted in `/media/usb/`

{{< command >}} 
sudo cp -acp $TEMP/{master.tomb, master.key, subkey.tomb, subkey.key, public.asc} /media/usb/
{{< /command >}} 

`lsblk` outputs:
```bash
NAME        FSTYPE      UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda                                                                         
├─sda1      btrfs       944b1c42-56ea-43f1-9945-a40354dcd1be   50.8G    10% /
└─sda2      btrfs       d68c9b6c-1dee-4f53-b43f-b2a2dbb15472  103.5G    74% /opt
sdb         crypto_LUKS ac9abe74-2769-4bb8-9805-5910ce7d3262                
└─usb       btrfs       d4d188b9-00ef-49db-aedd-a07fbd80f194   15.3G     0% /media/usb
```

{{< command >}} 
sudo umount /media/usb
{{< /command >}} 

`lsblk` outputs:
```bash
NAME        FSTYPE      UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda                                                                         
├─sda1      btrfs       944b1c42-56ea-43f1-9945-a40354dcd1be   50.8G    10% /
└─sda2      btrfs       d68c9b6c-1dee-4f53-b43f-b2a2dbb15472  103.5G    74% /opt
sdb         crypto_LUKS ac9abe74-2769-4bb8-9805-5910ce7d3262                
└─usb       btrfs       d4d188b9-00ef-49db-aedd-a07fbd80f194  
```
{{< command >}} 
sudo cryptsetup close usb
{{< /command >}} 

`lsblk` outputs:
```bash
NAME        FSTYPE      UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda                                                                         
├─sda1      btrfs       944b1c42-56ea-43f1-9945-a40354dcd1be   50.8G    10% /
└─sda2      btrfs       d68c9b6c-1dee-4f53-b43f-b2a2dbb15472  103.5G    74% /opt
sdb         crypto_LUKS ac9abe74-2769-4bb8-9805-5910ce7d3262                
```

{{< command >}} 
udisksctl power-off -b /dev/sdb
{{< /command >}} 

`lsblk` outputs:
```bash
NAME        FSTYPE      UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda                                                                         
├─sda1      btrfs       944b1c42-56ea-43f1-9945-a40354dcd1be   50.8G    10% /
└─sda2      btrfs       d68c9b6c-1dee-4f53-b43f-b2a2dbb15472  103.5G    74% /opt
```

