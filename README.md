# vaultx – A Minimal Offline Password Manager in Bash

vaultx is a lightweight, secure, and offline password manager written entirely in Bash. It uses OpenSSL for encryption and supports safe storage, auditing, and retrieval of credentials locally—no network access or cloud sync required.

---

## Features

- Save encrypted password entries with optional username and note
- Uses AES-256-CBC encryption with PBKDF2 (200,000 iterations)
- Secure metadata: creation timestamp, label, optional note
- Strong lockout policy on repeated failed login attempts
- Integrity verification via HMAC (SHA-256)
- Backup the entire vault into a ZIP archive
- Fuzzy-search entries with fzf
- Audit saved entries with modification times and notes

---

## Requirements

- bash (v4 or later)
- openssl
- zip
- fzf
- htpasswd (from apache2-utils or similar)
- (Optional) sudo or doas for remounting in vaultx for /proc safety.
  (Is only required if there is danger.)
- (Optional) wl-clip or xclip
- (Optional) qrencode to display qr-code in terminal to share it with your mobile device.

---

## Setup

Make the script executable and run it:

```bash
chmod +x pash.sh
./pash.sh
```

To use a custom vault directory, set the environment variable:

```bash
export VAULT_DIR="$HOME/.myvault"
```

---

## Usage

Launch the script and select an action from the interactive fzf menu:

- Save new entry: create an encrypted entry with password, optional username, and optional note  
- Decrypt entry: view stored credentials securely  
- Delete entry: permanently remove an entry  
- Backup vault: archive the entire vault folder as a ZIP file  
- Audit vault: display saved entries with timestamps and note previews  
- Exit: close the script  

---

## Security Notes

- **File Permissions**: A strict `umask 077` ensures that new files are only accessible by the owner, reducing exposure of sensitive data.

- **/proc Protection**: Checks the visibility of file descriptors:
  ```bash
  perms=$(stat -Lc "%a" /proc/self/fd)
  if (( ${perms: -1} )); then
    echo "WARNING: /proc/self/fd is world-readable!" >&2
  fi 
  
- If sudo or doas is installed, it will be remonuted.
- Master password is hashed and verified with htpasswd -B; only the hash is stored  
- Password data is encrypted using OpenSSL (aes-256-cbc) with salt and key stretching  
- Sensitive variables are wiped from memory using secure_unset()  
- Integrity is validated using per-entry HMACs (SHA-256 with OpenSSL)  
- Timeouts and lockout mechanism protect against brute-force attacks  

---

## Roadmap

Future enhancements may include:

- argon2 hash
- Sync and export of vaults via Git, GitHub, or GitLab  

---

## License

Licensed under the GNU General Public License v3.0 (https://www.gnu.org/licenses/gpl-3.0.html)
