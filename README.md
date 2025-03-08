# 🛠️ Docker Pentesting Lab - Beginner Workbook  

![Docker](https://img.shields.io/badge/Docker-✅-blue)
![Kali Linux](https://img.shields.io/badge/Kali_Linux-✅-blue)
![Pentesting](https://img.shields.io/badge/Pentesting-Lab-green)

🚀 **Goal**: Set up a Kali Linux-based penetration testing lab using Docker, volumes, and networking in a structured, beginner-friendly way.

---

## 📌 Features  
✅ Kali Linux with pre-installed pentesting tools  
✅ Vulnerable web applications for practice (DVWA)  
✅ MITM proxy setup for network traffic interception  
✅ Automated scanning scripts  
✅ Modular & easily expandable  

---

## 📂 Project Structure  

<details>
<summary>Click to Expand</summary>

```plaintext
pentest-lab/
│── docker-compose.yml           # Define services
│── Dockerfile                   # Kali Linux setup with tools
│── tools/                       
│   ├── wordlists/               # Placeholder for wordlists (SecLists, RockYou, etc.)
│   ├── exploits/                # Store custom exploits or downloaded ones
│   ├── reports/                 # Keep scan logs and test reports
│── scripts/                     
│   ├── auto_scan.sh             # Automate Nmap scanning
│   ├── proxy_setup.sh           # Automate BurpSuite proxy setup
│── configs/                     
│   ├── burpsuite.conf           # Custom BurpSuite config (optional)
│   ├── mitmproxy.conf           # Custom MITMProxy config (optional)
│── targets/                     
│   ├── vulnerable-web/          # Docker-based test targets (e.g., DVWA, Metasploitable)
│── README.md                    # Instructions & Documentation
```

</details>

---

## 🚀 Quick Start  

### 🛠️ Requirements  

- Docker & Docker Compose installed  
- Basic knowledge of penetration testing  

### ⏳ Installation  

1️⃣ **Clone this repository**  
```bash
git clone https://github.com/yourusername/docker-pentest-lab.git
cd docker-pentest-lab
```

2️⃣ **Build & Run the Lab**  
```bash
docker-compose up --build -d
```

3️⃣ **Access the Containers**  

- **Kali Linux**:  
  ```bash
  docker exec -it kali-pentest /bin/bash
  ```
- **DVWA Web Interface**:  
  Open [http://localhost:8080](http://localhost:8080) in a browser.

---

## 🔹 Step 1 - Create Kali Linux Dockerfile  

This Dockerfile sets up a minimal Kali Linux environment with essential pentesting tools.

```Dockerfile
FROM kalilinux/kali-rolling
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    python3 python3-pip python3-requests python3-bs4 \
    python3-cryptography vim nano tmux burpsuite dirb \
    sqlmap nikto nmap wfuzz hydra metasploit-framework \
    mitmproxy jq \
    --no-install-recommends && \
    rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY scripts/ /app/scripts/
COPY configs/ /app/configs/
CMD ["/bin/bash"]
```

---

## 🔹 Step 2 - Create `docker-compose.yml`  

This allows running multiple services (Kali, DVWA, MITM proxy) together.

```yaml
version: '3.8'

services:
  kali:
    build: .
    container_name: kali-pentest
    volumes:
      - ./tools:/tools
      - ./scripts:/scripts
      - ./configs:/configs
      - ./reports:/reports
    tty: true
    networks:
      - pentest-net

  dvwa:
    image: vulnerables/web-dvwa
    container_name: dvwa
    restart: always
    ports:
      - "8080:80"
    networks:
      - pentest-net

  mitmproxy:
    image: mitmproxy/mitmproxy
    container_name: mitmproxy
    ports:
      - "8081:8080"
    networks:
      - pentest-net
    command: mitmproxy --mode transparent

networks:
  pentest-net:
    driver: bridge
```

---

## 🔹 Step 3 - Add Useful Scripts  

### 📌 `scripts/auto_scan.sh` (Automate Nmap Scan on DVWA)  

```bash
#!/bin/bash
echo "[*] Running Nmap Scan on DVWA..."
nmap -A -T4 dvwa
```

**Make it executable**:  
```bash
chmod +x scripts/auto_scan.sh
```

**Run it inside the Kali container**:  
```bash
docker exec -it kali-pentest /scripts/auto_scan.sh
```

---

## 📜 License  

This project is open-source and available under the **MIT License**.

---

## 💬 Contributing  

Want to contribute? Feel free to submit pull requests!  
