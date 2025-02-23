# remoclipbo

## Overview

`remoclipbo` securely copies the contents of your clipboard to a remote machine without the need for virtual KVM software like Sharemouse, Synergy, etc.

## Requirements

- macOS using `bash`
- `pbcopy` and `pbpaste` commands must be available
- SSH access to the remote machine

See [remoclipbo GitHub repository](https://github.com/rkulla/remoclipbo) for how to automate it with shortcuts!

## Installation

1. Download the `remoclipbo` script and place it in your `$PATH` (e.g., `~/bin/`).
2. Make it executable:
   ```bash
   chmod +x ~/bin/remoclipbo
   ```
3. Edit the `REMOTE` variable in the script to point to the remote host.
   ```bash
   REMOTE='<user@host>:~/'
   ```

## Usage

### Copy clipboard to remote machine

On **both machines**, ensure `remoclipbo` is in your `$PATH` and executable.
Run:

```bash
remoclipbo
```

This writes the clipboard contents to `~/remote-clipboard.txt` and transfers it to the remote machine via `scp`.

**IMPORTANT:** After 15 seconds, it will clear the remote's ~/remote-clipboard.txt for security reasons. If you
have the launchagent installed below, that means it will also clear the remote's actual clipboard after 15 seconds.
If you don't want this behavior, adjust it in the ~/remoclipbo script itself. See the comments in the script.

### Retrieve clipboard from remote machine

To copy the remote clipboard contents back to your local clipboard, run:

```bash
remoclipbo -c
```

This reads `~/remote-clipboard.txt` and copies its contents to your local clipboard.

## Automation with launchd

To automatically update the clipboard when `remote-clipboard.txt` changes, create a launchd plist file:

```bash
vim ~/Library/LaunchAgents/com.user.remoteclipboard.plist
```

Paste the following (and make sure to edit the 2 paths with yours):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.user.remoteclipboard</string>

    <key>ProgramArguments</key>
    <array>
        <string>/Users/<your_username>/bin/remoclipbo</string>
        <string>-c</string>
    </array>

    <key>WatchPaths</key>
    <array>
        <string>/Users/<your_username>/remote-clipboard.txt</string>
    </array>

    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```

### Load the launch agent

```bash
launchctl load ~/Library/LaunchAgents/com.user.remoteclipboard.plist
```

To reload after editing:

```bash
launchctl unload ~/Library/LaunchAgents/com.user.remoteclipboard.plist
launchctl load ~/Library/LaunchAgents/com.user.remoteclipboard.plist
```

## Raycast Integration

NOTE: You don't have to use [raycast](https://www.raycast.com/), if you prefer other keyboard shortcut apps.

In `Settings>Extensions>Script Commands` click Add Directories, then add ~/.config/raycast/scripts/remoclipbo.sh and mkdir that locally and put my bash scripts in there and make them executable. Example of calling ~/bin/remoclipbo:

    #!/bin/bash
    # Required parameters:
    # @raycast.schemaVersion 1
    # @raycast.title Secure Copy Clipboard
    # @raycast.mode silent

    "$HOME/bin/remoclipbo"

Note that the comments in the raycast/scripts/remoclipbo.sh file seem to be required, and/or restart Raycast, before it will detect the script. It will then show the script under `Settings>Extensions>Script Commands` so you can add a hotkey to it. I map it to HYPER+C

Note: There's need to run 'Create Script Command' unless wanting to publish it publicly


- **Hyper+H** mapped to `Clipboard History` to see the clipboard contents.
- **Hyper+R** mapped to `Remote-clipboard.txt` quicklink to see `remote-clipboard.txt`.

## Notes

- Ensure SSH keys are set up for passwordless access.
- Adjust `sleep 1` in the script if clipboard contents are not transferring reliably.
- Use different `REMOTE` values for work and home machines to avoid conflicts.

## License

MIT License


