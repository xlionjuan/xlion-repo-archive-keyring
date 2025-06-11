# XLion Archive Keyring
Archive keyring for XLion repos

## Attestation

You need to have [gh](https://cli.github.com/) installed and logged in, it may not return anythng if running in CI, but will just return status code 0.

```sh
gh attestation verify --owner xlionjuan --predicate-type 'https://in-toto.io/attestation/release' xlion-repo-archive-keyring*
```

## Install

One line install command, `jq` is required.

```sh
sudo apt-get update && sudo apt-get install -y jq && url=$(curl -s https://api.github.com/repos/xlionjuan/xlion-repo-archive-keyring/releases/latest | jq -r '.assets[] | select(.name | endswith(".deb")) | .browser_download_url') && tmpfile="/tmp/$(basename "$url")" && curl -L "$url" -o "$tmpfile" && sudo dpkg -i "$tmpfile"
```

## Build

### Install deps

Requires Debain or its derivatives.

```sh
sudo apt install build-essential devscripts debhelper lintian
```

### Clone

Not need to say..

If you want to publish it to GitHub, fork it and clone your own fork.

### Build

Run

```
./run.sh
```

## Build your own keyring

### Change Debain control file/package name/branding

1. Edit [debian/control](debian/control) file, change `Source:` and `Package:` entry to your package name, change `Maintainer:` to your info, change `Description:` to you desired, change `Vcs-Git:` to your Git URL or remove it.
2. Go to [debian](debian/) directory, there is file that extension is `.install`, change the file name to the package name you specified.
3. Modify that `.install` file, change the file name to you desired, I personally recommend to name it to `{package name}.gpg`, but for historical reasons, I name it `xlion-repo.gpg`.
4. Edit [run.sh](run.sh) file, find and replace `xlion-repo-archive-keyring` to your package name, for the `gpg --dearmor --output` part, change the `.gpg` file name to specified at step 3.
5. Wipe all the contents at [debian/NEWS](debian/NEWS) if you don't need it.

### Prepare your public key

Put all armor format of public keys to [pubkeys](pubkeys) directory, file name doesn't matter, just for easier identify, the armor format of publuc key can be exported by:

```sh
gpg --export --armor {KEY ID}
```

Or

```sh
gpg --export --armor {KEY ID} > filename.asc
```

### changelog file modification

The package build requires the existence of [debian/changelog](debian/changelog) file, first, keep only one entry, like:

```
xlion-repo-archive-keyring (1.0) main; urgency=medium

  * Initial release, only current key included.

 -- Shihfu Juan <xlion@xlion.tw>  Tue, 10 Jun 2025 21:17:22 +0800
```

Change the package name to your package name to yours.

Look at the snippet ([debian/create-changelog.txt](debian/create-changelog.txt)), change the name and email to yours, than paste and run it at terminal, and then run `dch -i "Message.."`, after that, you will get a new entry like this: 

```
awesomerepo-archive-keyring (1.0ubuntu1) UNRELEASED; urgency=medium

  * Message..

 -- Awesome Guy <awesome@example.com>  Wed, 11 Jun 2025 18:53:48 +0800
```

It will put the maintainer info and current date automatically, so you don't need to modify by your own.

Copy the first line of last entry (Which may be `awesomerepo-archive-keyring (1.0) main; urgency=medium`) and replace it to the current entry, and change the version number to real version.

Remove the dummy entry.

### Build

Run

```
./run.sh
```

After done, manually unpack the `.deb` and check if the `.gpg` file is putting at right place and right name.

## GitHub Actions

### Fork and clone it and do the changes

*Also remember to push*

### Tag

Tag

```sh
# git tag {VERSION NUMBER}
# EX:
git tag 1.0
```

And text editor will open, you could just paste changelog.

Push tag

```sh
git push origin --tags
```

And GitHub Actions will do the release.

## Inspired by

https://git.proxmox.com/?p=proxmox-archive-keyring.git;a=tree