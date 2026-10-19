import random
import socket
import urllib3
import requests
import os
import subprocess
from concurrent.futures import ThreadPoolExecutor
import pyfiglet


logo=pyfiglet.figlet_format("qdeeh Catcher")
# تعطيل تحذيرات شهادات الـ SSL
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

NMAP_PATH = r"C:\Program Files (x86)\Nmap\nmap.exe"

IP_RANGES = [
    "45.33.0.0/16", "138.197.0.0/16", "95.216.0.0/16", "139.162.0.0/16",
    "45.76.0.0/16", "188.226.128.0/18", "2.80.0.0/14", "5.100.0.0/16",
    "31.166.0.0/15", "82.100.0.0/16", "197.32.0.0/14", "94.249.0.0/16",
    "73.0.0.0/8", "80.224.0.0/13", "91.198.0.0/16", "2.0.0.0/12",
    "216.82.240.0/20", "67.219.240.0/20", "85.158.136.0/21", "95.131.104.0/21",
    "46.226.48.0/21", "117.120.16.0/21", "2.52.0.0/14", "77.124.0.0/14",
    "87.68.0.0/14", "176.228.0.0/14", "46.120.0.0/15", "80.178.0.0/15",
    "84.94.0.0/15", "84.228.0.0/15", "31.154.0.0/16", "80.230.0.0/16",
    "213.8.0.0/16", "149.22.32.0/20", "149.22.48.0/20", "79.99.24.0/21",
    "146.88.208.0/21", "185.99.24.0/23", "185.99.26.0/23"
]

FALSE_POSITIVE_KEYWORDS = [
    "403 forbidden", "401 unauthorized", "access denied", "permission denied",
    "blocked", "error 1020", "cloudflare", "index of /", "default web page",
    "apache2 ubuntu default page", "welcome to nginx", "iis7", "cpanel successfully installed"
]

def cidr_to_random_ip(cidr):
    try:
        ip_part, mask_part = cidr.split('/')
        mask = int(mask_part)
        octets = list(map(int, ip_part.split('.')))
        ip_num = (octets[0] << 24) + (octets[1] << 16) + (octets[2] << 8) + octets[3]
        hosts_count = (1 << (32 - mask)) - 1
        if hosts_count <= 1: return ip_part
        random_offset = random.randint(1, hosts_count)
        random_ip_num = ip_num + random_offset
        return f"{(random_ip_num >> 24) & 255}.{(random_ip_num >> 16) & 255}.{(random_ip_num >> 8) & 255}.{random_ip_num & 255}"
    except:
        return f"{random.randint(45, 210)}.{random.randint(0, 255)}.{random.randint(0, 255)}.{random.randint(1, 254)}"

def generate_random_ip():
    return cidr_to_random_ip(random.choice(IP_RANGES))

def check_target_flow(task):
    ip, port, is_web = task
    
    # 1. الفحص الصاعق المبدئي - للتخلص من الـ IPs الميتة فوراً
    try:
        with socket.create_connection((ip, port), timeout=0.15):
            pass
    except:
        return # منفذ مغلق، تخطي فوراً دون إضاعة وقت
        
    # 2. إذا وصل الكود هنا، يعني أن المنفذ مفتوح وحي! الآن نتحقق بعمق أعلى ومهلة مريحة
    try:
        if not is_web:
            verdict = "Responsive Device Node / Open Infrastructure"
        else:
            protocol = "https" if port in [443, 8443] else "http"
            url = f"{protocol}://{ip}:{port}"
            
            response = requests.get(url, timeout=1.0, verify=False, allow_redirects=True)
            status = response.status_code
            
            if status in [401, 403, 404] or status >= 500:
                return
                
            page_content = response.text.lower()
            if any(kw in page_content for kw in FALSE_POSITIVE_KEYWORDS):
                return
                
            server_header = response.headers.get("Server", "Unknown")
            verdict = f"Real Active Web Service [Status: {status} | Server: {server_header}]"

        # 3. تشغيل Nmap على الهدف المؤكد بنسبة 100% فقط
        nmap_exe = NMAP_PATH if os.path.exists(NMAP_PATH) else "nmap"
        cmd = [nmap_exe, "-p", str(port), "-sV", "-Pn", "-T4", "--open", "--host-timeout", "3s", ip]
        scan = subprocess.run(cmd, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True, errors='ignore')
        output = scan.stdout
        
        if "open" in output:
            print("\n" + "🟢" * 35)
            print(f"📡 VERIFIED LIVE TARGET: {ip}:{port}")
            print(f"🖥️ Identity Info : {verdict}")
            print(f"📝 DETAILED LIVE NMAP REPORT:\n")
            print(output.strip())
            print("🟢" * 35 + "\n")
    except:
        pass

def run_turbo_scan(max_workers=500):
    print("==================================================")
    print("⚡ HYPER-SPEED MULTI-STAGE SCANNER ACTIVATED")
    print(f"📡 Parallel Pipeline Core: {max_workers} Threads")
    print("🖥️ Mode: Zero-Delay Filtering for Maximum Throughput")
    print("==================================================")
    
    ports_pool = [
        (80, True), (443, True), (8080, True), (8443, True),
        (22, False), (3389, False), (445, False), (23, False)
    ]
    
    with ThreadPoolExecutor(max_workers=max_workers) as executor:
        while True:
            try:
                # توليد حزمة ضخمة من المهام وعمل مسح متوازي فائق السرعة لها
                tasks = []
                for _ in range(200):
                    random_ip = generate_random_ip()
                    for port, is_web in ports_pool:
                        tasks.append((random_ip, port, is_web))
                
                executor.map(check_target_flow, tasks)
            except KeyboardInterrupt:
                print("\n[!] Scanning stopped by user.")
                break
            except Exception:
                continue

if __name__ == "__main__":
    print(logo)
    run_turbo_scan(max_workers=500) # رفع عدد الخيوط لـ 500 لأن الفحص الأولي لا يستهلك قدرة الشبكة
