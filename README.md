# 🐧 Odoo 12 Deployment on Ubuntu 24.04

This repository contains configuration and setup files to deploy **Odoo 12** on **Ubuntu 24.04**, using **Python 3.7** and **PostgreSQL 11**.

---

## 📦 Stack

- **Operating System:** Ubuntu 24.04 LTS
- **ERP System:** Odoo 12
- **Programming Language:** Python 3.7
- **Database:** PostgreSQL 11

---

## ⚙️ Configuration

Below is the configuration content from `Ubuntu24_odoo12_conf`:

```
Step1:
 Update system--
sudo apt update && sudo apt upgrade -y
 
step2:
Install dependencies--
sudo apt install git curl gcc g++ make python3-pip libxml2-dev libxslt1-dev zlib1g-dev libsasl2-dev libldap2-dev build-essential libjpeg-dev libpq-dev libffi-dev libtiff5-dev libopenjp2-7-dev libwebp-dev libharfbuzz-dev libfribidi-dev libxcb1-dev libssl-dev libjpeg8-dev -y

Step3:
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt update
sudo apt install python3.7 python3.7-venv python3.7-dev -y
alias python=python3.7


Step4:
echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" | \
sudo tee /etc/apt/sources.list.d/pgdg.list

wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | \
sudo apt-key add -

Step5:
sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor | \
sudo tee /etc/apt/keyrings/postgresql.gpg > /dev/null

echo "deb [signed-by=/etc/apt/keyrings/postgresql.gpg] http://apt.postgresql.org/pub/repos/apt/ noble-pgdg main" | \
sudo tee /etc/apt/sources.list.d/pgdg.list

sudo apt update

sudo apt install postgresql-11

Step6:
Create Odoo PostgreSQL user
sudo -u postgres createuser --createdb --username postgres --no-createrole --no-superuser --pwprompt odoo12

It prompts you twice to enter and confirm a password for the PostgreSQL user odoo12.
Enter password for new role: 
Enter it again:


Step7:
Create Odoo system user
sudo useradd -m -d /opt/odoo12 -U -r -s /bin/bash odoo12
sudo su - odoo12

Step8:
Clone Odoo 12 and setup environment
git clone https://www.github.com/odoo/odoo --depth 1 --branch 12.0 odoo-12.0
cd odoo-12.0
python3.7 -m venv venv
source venv/bin/activate
pip install wheel
pip install -r requirements.txt
deactivate
exit

step9:
Install Wkhtmltopdf (for PDF reports)
sudo apt install xfonts-75dpi xfonts-base -y
wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb
sudo dpkg -i wkhtmltox_0.12.5-1.bionic_amd64.deb

Step10:
Configuration File
sudo nano /etc/odoo12.conf

code::
[options]
admin_passwd = admin123
db_host = False
db_port = False
db_user = odoo12
db_password = your_postgres_password
addons_path = /opt/odoo12/odoo-12.0/addons
logfile = /var/log/odoo12/odoo.log


Step11:

Create log directory
sudo mkdir /var/log/odoo12
sudo chown odoo12:root /var/log/odoo12

Step12:
Create service File

sudo nano /etc/systemd/system/odoo12.service

code::

[Unit]
Description=Odoo 12
Requires=postgresql.service
After=network.target postgresql.service

[Service]
Type=simple
SyslogIdentifier=odoo12
PermissionsStartOnly=true
User=odoo12
Group=odoo12
ExecStart=/opt/odoo12/odoo-12.0/venv/bin/python3 /opt/odoo12/odoo-12.0/odoo-bin -c /etc/odoo12.conf
StandardOutput=journal+console

[Install]
WantedBy=multi-user.target


Step13:
Start and Enable Odoo 12

sudo systemctl daemon-reload
sudo systemctl enable --now odoo12

 Check service status:
 
 sudo systemctl status odoo12

Step14:
http://<ec2-ip>:8069 

```

---

## 🚀 How to Use

1. **Clone this repository:**
   ```bash
   git clone https://github.com/your-username/odoo12-ubuntu24.git
   cd odoo12-ubuntu24
   ```

2. **Review and customize configuration files if needed.**

3. **Run your setup scripts (if available).**

4. **Access Odoo:**
   ```
   http://<your-server-ip>:8069
   ```

---

## 🛠 Additional Notes

Ensure all system dependencies are installed, including:
- Python 3.7
- PostgreSQL 11
- Wkhtmltopdf (for PDF reports in Odoo)

---

## 🙋 Support

For questions or issues, please open a GitHub issue in this repository.
