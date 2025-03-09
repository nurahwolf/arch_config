# arch_config
A 'baseline' configuration for Arch Linux. Intended to be used with my dotfiles + mkosi

#### Pacman Errors?

Getting something like the below error on update?

```fish
pacman -Syu

error: blackarch: signature from "Levon 'noptrix' Kayan (BlackArch Developer) <noptrix@nullsecurity.net>" is unknown trust
error: failed to synchronize all databases (invalid or corrupted database (PGP signature))
```

That's because the cachyos / blackarch repos are not trusted. You can fix that with this snippet (as root):

```fish
set GPG_CONF '/etc/pacman.d/gnupg/gpg.conf'
set WORKING_DIR (mktemp -d /tmp/bootstrap.XXXXXXXX)

cd "$WORKING_DIR" || err "Could not enter directory $WORKING_DIR"

curl -O 'https://www.blackarch.org/keyring/blackarch-keyring.pkg.tar.zst'
curl -O 'https://mirror.cachyos.org/cachyos-keyring-2-1-any.pkg.tar.zst'

if ! grep -q 'allow-weak-key-signatures' $GPG_CONF
	echo 'allow-weak-key-signatures' >> $GPG_CONF
end

pacman --config /dev/null -U blackarch-keyring.pkg.tar.zst
pacman --config /dev/null -U cachyos-keyring-2-1-any.pkg.tar.zst
```

Please note that the above enables `allow-weak-key-signatures` due to the use of `SHA1`... Blame the blackarch developers.
If this fails, you can locally sign the keys. Fetch them with `pacman-key --recv-keys` if needed. At time of writing these are as follows:

```fish
pacman-key -lsign F9A6E68A711354D84A9B91637533BAFE69A25079 # BlackArch
pacman-key -lsign 882DCFE48E2051D48E2562ABF3B607488DB35A47 # CachyOS
```
