# Codeforces Contest Notifier (Node.js)

A simple Node.js script to get desktop notifications for upcoming Codeforces contests on Arch Linux.

## Features

* Fetches upcoming contests from the Codeforces API.
* Sends desktop notifications when a contest is within a configurable time window.
* Runs as a background service using `systemd`.
* Avoids duplicate notifications for the same contest.

## Requirements

* Node.js (v14+ recommended)
* npm (Node Package Manager)  
* An Arch Linux desktop environment (for desktop notifications via `node-notifier`)

## Installation & Setup

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/neeli-satwik/codeforces-notifier-js.git](https://github.com/neeli-satwik/codeforces-notifier-js.git)
    cd codeforces-notifier-js
    ```

2.  **Install dependencies:**
    ```bash
    npm install axios node-notifier (third-party libraries that i use)
    ```

3.  **Run Manually (for testing):**
    ```bash
    node iNdex.js
    ```
    *This will stop when you close the terminal.*

4.  **Run as a Background Service with `systemd` (Recommended):**
    This ensures the notifier runs automatically on startup and stays in the background.

    a.  **Create a systemd user service file:**
        ```bash
        mkdir -p ~/.config/systemd/user/
        nano ~/.config/systemd/user/codeforces-notifier.service
        ```

    b.  **Paste the following content into the file:**
        (Remember to replace `<YOUR_LINUX_USERNAME>` with your actual Arch Linux username)
        ```ini
        [Unit]
        Description=Codeforces Contest Notifier (JavaScript)
        After=network-online.target graphical.target display-manager.service

        [Service]
        ExecStart=/usr/bin/node /home/<YOUR_LINUX_USERNAME>/codeforces-notifier-js/index.js
        WorkingDirectory=/home/<YOUR_LINUX_USERNAME>/codeforces-notifier-js
        Restart=on-failure
        StandardOutput=journal
        StandardError=journal

        [Install]
        WantedBy=default.target
        ```
        Save and close the file.

    c.  **Enable and Start the service:**
        ```bash
        systemctl --user daemon-reload
        systemctl --user enable codeforces-notifier.service
        systemctl --user start codeforces-notifier.service
        ```

    d.  **Check status and logs (optional):**
        ```bash
        systemctl --user status codeforces-notifier.service
        journalctl --user -u codeforces-notifier.service -f
        ```

## Configuration

You can modify the following variables at the top of `index.js`:

* `notification_windows_inminutes`: Time window (in minutes) before a contest starts to receive a notification. (Default: `60`)
* `polling_interval`: How often (in milliseconds) the script checks for new contests. (Default: `1800 * 1000` = 30 minutes)
