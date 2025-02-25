# remoclipbo

## Overview

`remoclipbo` securely copies the contents of your clipboard to a remote machine. 

## Requirements

- macOS using `bash`
- `pbcopy` and `pbpaste` commands must be available
- SSH access to the remote machine

See [remoclipbo GitHub repository](https://github.com/rkulla/remoclipbo) for how to automate it with shortcuts!

## Installation

1. Download `remoclipbo` and place it in your `$PATH` (e.g., `~/bin/`).
2. Make it executable:
   ```bash
   chmod +x ~/bin/remoclipbo
   ```
3. Edit the `REMOTE` variable in the script to point to the remote host.
   ```bash
   REMOTE='<user@host>:~/'
   ```

## Usage

First make sure to EDIT the remoclipbo script to update the REMOTE variable at the top of the script to point to the `user@host` you want to copy to. Also make sure `remoclipbo` is in your `$PATH` and executable. 

### Copy clipboard contents to remote machine

```sh
remoclipbo
```
This will paste the contents of your clipboard to `~/remote-clipboard.txt`  
and then `scp` it to the remote machine's `~/remote-clipboard.txt`.

### Copy and clear the remote clipboard after a delay
```sh
remoclipbo --clear-after-secs[=<n secs>]
```
This will also clear the remote clipboard after `<n secs>`.  
If no value is provided, it defaults to **15 seconds**.

### Retrieve local ~/remote-clipboard.txt contents into local clipboard
```sh
remoclipbo -c
```
This will read `~/remote-clipboard.txt` and copy its contents to the clipboard.  
Useful if you ran this from another machine and want to put the contents  
of `~/remote-clipboard.txt` into your clipboard.  
Ideally, you will automate this part. See `README.md`.

### Verify remote clipboard contents without copying
```sh
remoclipbo -verify
```
This will print the contents of the remote machine’s `~/remote-clipboard.txt`  
without copying anything. Useful for verifying what was sent to the remote.

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


