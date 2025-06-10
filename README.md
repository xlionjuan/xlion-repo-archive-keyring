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