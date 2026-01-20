Tautulli Podman Quadlet Deployment
==================================

**Disclaimer:** I am not affiliated with, associated, authorized, endorsed by, or in any way officially connected with the Tautulli or Plex projects.

This guide demonstrates how to deploy **Tautulli** as a System Podman Quadlet on Ubuntu 25.10.

**Crucial Requirement:** Tautulli must be installed as a **System Quadlet** (running as root/system level) to properly communicate with the Plex System Quadlet and access the required Plex log files.

System Information
------------------

*   **Tested Environment:** Ubuntu 25.10
*   **Podman Version:** 5.4.x
*   **Dependency:** Requires the Plex Quadlet to be named `plex.service`

1\. The Quadlet File (tautulli.container)
-----------------------------------------

Download or create the `tautulli.container` file. Ensure the `After=plex.service` directive is present in the `[Unit]` section to ensure correct startup order.

2\. Installation & Setup
------------------------

### Step A: Clean up existing containers

Remove any legacy Docker or manual Podman Tautulli instances.

### Step B: Verify User IDs

Ensure the `PUID` and `PGID` in the file match your host user (run `id` to check). This ensures Tautulli can read the Plex logs without permission errors.

### Step C: Move and Edit

Move the file to the systemd directory and update your volume paths:

`sudo mkdir -p /etc/containers/systemd`
`sudo mv tautulli.container /etc/containers/systemd/`
`sudo nano /etc/containers/systemd/tautulli.container` to edit the container

_Note: Make sure the path to your Plex logs volume is correct._

3\. Activation
--------------

Reload the systemd daemon to generate the service and start Tautulli:

`sudo systemctl daemon-reload`
`sudo systemctl start tautulli`

4\. Post-Install Verification
-----------------------------

### Verify Service Link

Check if Tautulli successfully waited for Plex:

`sudo systemctl status tautulli`

### Verify Log Access

To ensure Tautulli can actually see the Plex logs for monitoring, check the directory inside the container:

`podman exec -it tautulli ls -l /logs`

### Check Timezone

Confirm the container clock matches your host:

`podman exec -it tautulli date`

5\. Updating
------------

Update Tautulli alongside your other media services:

sudo podman auto-update

_Tautulli will now start automatically whenever the system boots, specifically after the Plex service is initialized._
