# Troubleshooting Caddy: A Guide to Running and Renewing SSL

This guide outlines a methodical approach to diagnosing and resolving common issues with Caddy, including startup failures and problems with SSL certificate renewal. The process involves systematically checking Caddy's status, configuration, and permissions.

---

## 1. Initial Caddy Status Check

Start by verifying Caddy's operational status and location.

* **Check the Caddy process:**
    ```bash
    which caddy
    ```
    This command shows the path to the Caddy executable.
* **Get Caddy's version:**
    ```bash
    caddy version
    ```
    This helps in identifying potential version-specific issues.
* **Find Caddy-related files:**
    ```bash
    sudo find /etc -name “*caddy*” -o -name “Caddyfile” 2>/dev/null
    ```
    This command helps locate Caddy's configuration and related files.
* **View the Caddyfile:**
    ```bash
    sudo cat /etc/caddy/Caddyfile
    ```
    Review the Caddyfile for any syntax errors or misconfigurations.

---

## 2. Diagnosing SSL and Directory Issues

SSL certificate renewal and Caddy's ability to write to its data directory are frequent points of failure.

* **List certificate files:**
    ```bash
    sudo ls -la /var/lib/caddy/.local/share/caddy/certificates/
    ```
    Check if certificates exist and if their permissions are correct.
* **Find certificate and ACME files:**
    ```bash
    sudo find /var/lib/caddy -name “*certificate*” -o -name “*acme*” 2>/dev/null
    ```
    This helps locate all related SSL and ACME challenge files.
* **List Caddy's data directory contents:**
    ```bash
    sudo ls -la /var/lib/caddy
    ```
    Verify the permissions and ownership of the Caddy data directory.

---

## 3. Reviewing Logs and Validation

Logs are crucial for understanding why Caddy might be failing.

* **Check Caddy service logs:**
    ```bash
    sudo journalctl -u caddy -no-page -n 50
    ```
    Examine the last 50 lines of the Caddy service journal for error messages.
* **Validate the Caddyfile:**
    ```bash
    sudo caddy validate --config /etc/caddy/Caddyfile
    ```
    A successful validation confirms that the Caddyfile syntax is correct.

---

## 4. Remediation Steps

If errors were found, follow these steps to fix common issues.

* **Restart the Caddy service:**
    ```bash
    sudo systemctl restart caddy
    ```
    A simple restart can resolve temporary glitches.
* **View the Caddy service unit file:**
    ```bash
    sudo cat /etc/systemd/system/caddy.service
    ```
    This file defines how Caddy runs and under which user.
* **Test Caddy with its configuration:**
    ```bash
    sudo /usr/local/bin/caddy run --environ --config /etc/caddy/Caddyfile
    ```
    This command runs Caddy in the foreground, providing real-time output that can reveal subtle errors.
* **Clear old certificate data:**
    ```bash
    sudo rm -rf /root/.local/share/caddy/certificates/
    sudo rm -rf /root/.local/share/caddy/locks/
    ```
    Sometimes, corrupted or outdated certificates can prevent renewal. Deleting them forces Caddy to request new ones.
* **Format the Caddyfile:**
    ```bash
    sudo caddy fmt --overwrite /etc/caddy/Caddyfile
    ```
    This command standardizes the formatting of the Caddyfile, which can help in spotting syntax issues.

---

## 5. Ensuring Proper User and Permissions

Incorrect user permissions are a very common cause of Caddy failures. The best practice is to run Caddy as a dedicated, unprivileged system user.

* **Add a dedicated system user for Caddy:**
    ```bash
    sudo user add --system --home /var/lib/caddy --create-home --shell /usr/sbin/nologin caddy
    ```
* **Set proper ownership:**
    ```bash
    sudo chown -R caddy:caddy /var/lib/caddy
    ```
    This ensures Caddy can read and write to its data directory.

---

## 6. Fixing the Systemd Service File

The systemd service file dictates Caddy's behavior. An incorrect configuration can cause it to fail silently.

* **Create or correct the systemd service file:**
    ```bash
    sudo tee /etc/systemd/system/caddy.service > /dev/null << 'EOF'
    [Unit]
    Description=Caddy Web Server
    Documentation=[https://caddyserver.com/docs/](https://caddyserver.com/docs/)
    After=network.target network-online.target
    Requires=network-online.target

    [Service]
    Type=notify
    User=caddy
    Group=caddy
    ExecStart=/usr/local/bin/caddy run --environ --config /etc/caddy/Caddyfile
    ExecReload=/usr/local/bin/caddy reload --config /etc/caddy/Caddyfile --force
    TimeoutStopSec=5s
    LimitNOFILE=1048576
    LimitNPROC=1048576
    PrivateTmp=true
    ProtectSystem=full
    AmbientCapabilities=CAP_NET_BIND_SERVICE
    NoNewPrivileges=true

    [Install]
    WantedBy=multi-user.target
    EOF
    ```
* **Reload systemd:**
    ```bash
    sudo systemctl daemon-reload
    ```
    This command makes systemd aware of the new or changed service file.

---

## 7. Final Verification and Cleanup

* **Check logs for final errors:**
    ```bash
    sudo journalctl -xeu caddy.service --no-pager -n 20
    ```
    Review the most recent logs to confirm the service is starting without issues.
* **Check listening ports:**
    ```bash
    sudo lsof -i :2019
    ```
    This command verifies that Caddy is listening on its default management port.
* **Kill any old Caddy processes:**
    ```bash
    sudo pkill -f caddy
    ```
    This ensures that no conflicting processes are running.
* **Start the Caddy service:**
    ```bash
    sudo systemctl start caddy
    ```
* **Test connectivity and SSL:**
    ```bash
    sleep 5 && curl -I [https://api.nexplace.jp](https://api.nexplace.jp)
    echo | openssl s_client -servername api.nexplace.jp -connect api.nexplace.jp:443 2>/dev/null | openssl x509 -noout -dates
    ```
    These commands confirm that the site is accessible and that the SSL certificate is valid and has been renewed.
* **Enable Caddy to start on boot:**
    ```bash
    sudo systemctl enable caddy
    ```
    This final step ensures that Caddy will automatically start whenever the system reboots.