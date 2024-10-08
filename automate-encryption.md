# Automate folder encryption using GPG on linux

1. Install dependencies: Ensure `gpg` and `inotify-tools` (for monitoring file changes) are installed.
```bash
sudo dnf install gnupg inotify-tools
```

2. Generate key pair: Run this command to make a new key pair to use in encryption `choose default options`.
```bash
‫gpg ‪--full-generate-key
```

3. Create a script: You can write a script that monitors the folder, encrypts new files, and deletes the original.\
save this file as: `encrypt-folder.sh` or anything else and put it in `/usr/local/bin/`.
```bash
#!/bin/bash

WATCHED_FOLDER="/path/to/folder"
GPG_RECIPIENT="example@email.com"

# Function to encrypt and delete the file
encrypt_and_delete() {
    local file="$1"
    
    # Check if the file already has the .gpg extension
    if [[ "$file" != *.gpg ]]; then
        gpg -r "$GPG_RECIPIENT" -e "$file"
        if [ $? -eq 0 ]; then
            rm "$file"
            echo "Encrypted and deleted: $(basename "$file")"
        fi
    fi
}

# Use inotifywait to monitor the folder for new files
inotifywait -m -e close_write --format "%f" "$WATCHED_FOLDER" | while read file; do
    encrypt_and_delete "$WATCHED_FOLDER/$file"
done
```

5. Change permissions for this file to make it executable.
```bash
sudo chmod +x /usr/local/bin/encrypt-folder.sh
```

6. Create a systemd service file by running this.
```bash
sudo nano /etc/systemd/system/encrypt-folder.service
```

7. Inside the file, add the following content:
```bash
[Unit]
Description=Automatically encrypt files in folder
After=network.target

[Service]
ExecStart=/usr/local/bin/encrypt-folder.sh
Restart=on-failure
User=your_computer_username
Group=your_computer_username

[Install]
WantedBy=multi-user.target
```

8. Reload systemd to recognize the new service
```bash
sudo systemctl daemon-reload
```

9. Enable the service to start on boot
```bash
sudo systemctl enable encrypt-folder.service
```

10. Start the service
```bash
sudo systemctl start encrypt-folder.service
```

11. Check the status of the service
```bash
sudo systemctl status encrypt-folder.service
```

### In case of errors while starting the service or checking the status

1. Install the required tools:
```bash
sudo dnf install policycoreutils policycoreutils-python-utils selinux-policy-devel
```

2. Generate the policy: After checking the logs, generate a custom policy `run this command in an empty directory because it will generate two files`:
```bash
sudo ausearch -m avc -ts recent | audit2allow -M encrypt_folder_policy
```

3. Install the Policy Module
```bash
sudo semodule -i encrypt_folder_policy.pp
```

4. Verify the Policy Installation
```bash
sudo semodule -l | grep encrypt_folder_policy
```

5. Test the Service
```bash
sudo systemctl restart encrypt-folder.service
```

6. Check the status to ensure it's active:
```bash
sudo systemctl status encrypt-folder.service
```