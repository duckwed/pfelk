## Custom Installation Guide (pfSense/OPNsense) + Elastic Stack 

## Table of Contents

- [Preparation](#preparation)
- [Installation](#installation)
- [Configuration](#configuration)

# Preparation

### 1. Add Maxmind Repository
```
sudo add-apt-repository ppa:maxmind/ppa
```

### 2. Download and install the public GPG signing key
```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```

### 3. Download and install apt-transport-https package
```
sudo apt-get install apt-transport-https
```

### 4. Add Elasticsearch|Logstash|Kibana Repositories (version 7+)
```
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
```

### 5. Update
```
sudo apt-get update
```

### 6. Install Java 11 LTS
```
sudo apt install openjdk-11-jre
```

### 7. Install Maxmind
```
sudo apt install geoipupdate
```

### 8. Configure Maxmind
- Create a Max Mind Account @ https://www.maxmind.com/en/geolite2/signup
- Login to your Max Mind Account; navigate to "My License Key" under "Services" and Generate new license key
```
sudo nano /etc/GeoIP.conf
```
- Modify lines 7 & 8 as follows (without < >):
```
AccountID <Input Your Account ID>
LicenseKey <Input Your LicenseKey>
```
- Modify line 13 as follows:
```
EditionIDs GeoLite2-City GeoLite2-Country GeoLite2-ASN
```

### 9. Download Maxmind Databases
```
sudo geoipupdate -d /usr/share/GeoIP/
```

### 10. Add cron (automatically updates Maxmind everyweek on Sunday at 1700hrs)
```
sudo nano /etc/cron.weekly/geoipupdate
```
- Add the following and save/exit
```
00 17 * * 0 geoipupdate -d /usr/share/GeoIP
```

# Installation
- Elasticsearch v7+ | Kibana v7+ | Logstash v7+

### 11. Install Elasticsearch|Kibana|Logstash
```
sudo apt-get install elasticsearch && sudo apt-get install kibana && sudo apt-get install logstash
```

# Configure Kibana|v7+

### 12. Configure Kibana
```
sudo nano /etc/kibana/kibana.yml
```

### 13. Modify host file (/etc/kibana/kibana.yml)
- server.port: 5601
- server.host: "0.0.0.0"

# Configure Logstash|v7+

### 14. Change Directory
```
cd /etc/logstash/conf.d
```

### 15. Download the following configuration files
```
sudo wget https://raw.githubusercontent.com/3ilson/pfelk/master/conf.d/01-inputs.conf
```
```
sudo wget https://raw.githubusercontent.com/3ilson/pfelk/master/conf.d/05-firewall.conf
```
```
sudo wget https://raw.githubusercontent.com/3ilson/pfelk/master/conf.d/30-geoip.conf
```
```
sudo wget https://raw.githubusercontent.com/3ilson/pfelk/master/conf.d/50-outputs.conf
```
### 15a. (Optional) Download the following configuration files
```
sudo wget https://raw.githubusercontent.com/3ilson/pfelk/master/conf.d/10-others.conf
```
```
sudo wget https://raw.githubusercontent.com/3ilson/pfelk/master/conf.d/20-suricata.conf
```
```
sudo wget https://raw.githubusercontent.com/3ilson/pfelk/master/conf.d/25-snort.conf
```
```
sudo wget https://raw.githubusercontent.com/3ilson/pfelk/master/conf.d/40-dns.conf
```
```
sudo wget https://raw.githubusercontent.com/3ilson/pfelk/master/conf.d/45-cleanup.conf
```

### 16. Make Patterns Folder
```
sudo mkdir /etc/logstash/conf.d/patterns
```

### 17. Navigate to Patterns Folder
```
cd /etc/logstash/conf.d/patterns/
```

### 18. Download the grok pattern file
```
sudo wget https://raw.githubusercontent.com/3ilson/pfelk/master/conf.d/patterns/pfelk.grok
```

### 19. Make Template Folder
```
sudo mkdir /etc/logstash/conf.d/template
```

### 20. Navigate to Template Folder
```
cd /etc/logstash/conf.d/template/
```

### 21. Download Template
```
sudo wget https://raw.githubusercontent.com/3ilson/pfelk/master/conf.d/template/pf-geoip-template.json
```

### 22. Enter your pfSense/OPNsense IP address (01-inputs.conf)
```
Change line 12; the "if [host] =~ ..." should point to your pfSense/OPNsense IP address
Change line 15; rename "firewall" (OPTIONAL) to identify your device (i.e. backup_firewall)
Change line 18-27; (OPTIONAL) to point to your second PF IP address or ignore
```

### 23. Revise/Update w/pf IP address (01-inputs.conf)
```
For pfSense uncommit line 34 and commit out line 31
For OPNsense uncommit line 31 and commit out line 34
```
### 24. Complete Configuration --> [Configuration](configuration.md)
