# 🚀 Frappe + Bench Installation Guide (2025)

A complete, step-by-step guide to install **Frappe Framework**, **Bench**, **MariaDB**, **Redis**, and all dependencies on **Ubuntu / WSL2 / Linux**.

This guide is fully working and tested in **2025**.

---

## 📌 1. System Update

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git curl wget build-essential software-properties-common

📌 2. Install Python & Required Tools
sudo apt install -y python3 python3-dev python3-setuptools python3-pip python3-venv

📌 3. Install NodeJS (Recommended: Node 18 LTS)
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

📌 4. Enable Corepack & Install Yarn
sudo corepack enable
sudo corepack prepare yarn@stable --activate
yarn --version

📌 5. Install Redis
sudo apt install -y redis-server
sudo service redis-server start
sudo systemctl enable redis-server

📌 6. Install MariaDB (Database)
sudo apt install -y mariadb-server mariadb-client
sudo service mysql start

📌 7. Fix MariaDB Authentication (Important)

MariaDB uses auth_socket by default which breaks Bench DB setup.

Create a dedicated DB admin account:

sudo mysql


Inside MariaDB prompt:

CREATE USER 'frappeuser'@'localhost' IDENTIFIED BY 'FrappeDBPass!';
GRANT ALL PRIVILEGES ON *.* TO 'frappeuser'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;


✔ Avoids 1698 “Access Denied” errors
✔ Works perfectly with Bench

📌 8. Install Bench CLI
pip3 install frappe-bench
bench --version


If bench not detected:

export PATH="$HOME/.local/bin:$PATH"


(Add to ~/.bashrc for persistence.)

📌 9. Create a Bench Instance
bench init frappe-bench

📌 10. Create Your First Frappe Site
cd frappe-bench
bench new-site mysite.localhost


Inputs:

MySQL Super User: frappeuser

Password: FrappeDBPass!

Set Administrator password when asked

📌 11. Fix Hostname Mapping (Required)

Frappe serves sites based on hostname.

Linux / WSL:
sudo sh -c "echo '127.0.0.1 mysite.localhost' >> /etc/hosts"

Windows (if accessing via Windows browser during WSL use):

Edit:

C:\Windows\System32\drivers\etc\hosts


Add:

127.0.0.1 mysite.localhost


Or if WSL shows a specific IP (example):

172.29.51.143 mysite.localhost

📌 12. Start Bench
bench start


You should see:

Running on http://127.0.0.1:8000
Running on http://<WSL-IP>:8000


Open your site:

http://mysite.localhost:8000/login


Login:

Username: Administrator

Password: (the one you set)

🎉 Your Frappe site is now live!

⚙ Optional Add-ons
📌 13. Install ERPNext (Optional)
bench get-app erpnext --branch version-15
bench --site mysite.localhost install-app erpnext

📌 14. Enable Scheduler (Optional)
bench --site mysite.localhost set-config enable_scheduler true
bench restart

📌 15. Fix Redis "vm.overcommit_memory" Warning (Optional)
echo 'vm.overcommit_memory=1' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

🛠 Common Errors & Fixes
❌ 404 Not Found

✔ Use hostname URL, not IP

http://mysite.localhost:8000/login

❌ Access denied for user 'root'@'localhost' (1698)

✔ Use your created user:

frappeuser / FrappeDBPass!

❌ bench not found

✔ Add to PATH:

export PATH="$HOME/.local/bin:$PATH"