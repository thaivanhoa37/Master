# 🚀 **HƯỚNG DẪN SETUP CHI TIẾT - ĐỀ TÀI TCP PERFORMANCE ANALYSIS**

---

## 📋 **MỤC LỤC**
1. [Yêu cầu hệ thống](#yêu-cầu-hệ-thống)
2. [Cài đặt môi trường cơ bản](#cài-đặt-môi-trường-cơ-bản)
3. [Cài đặt NS-3](#cài-đặt-ns-3)
4. [Cài đặt Wireshark và các công cụ](#cài-đặt-wireshark)
5. [Setup Repository](#setup-repository)
6. [Chạy thí nghiệm đầu tiên](#chạy-thí-nghiệm)
7. [Phân tích dữ liệu](#phân-tích-dữ-liệu)
8. [Troubleshooting](#troubleshooting)

---

## 💻 **1. YÊU CẦU HỆ THỐNG**

### **Hardware:**
```markdown
✅ Recommended:
├── CPU: Intel i5/i7 hoặc AMD Ryzen 5/7 (4+ cores)
├── RAM: 8GB+ (khuyến nghị 16GB)
├── Disk: 30GB+ free space
└── OS: Ubuntu 20.04/22.04 LTS (khuyến nghị nhất)

✅ Minimum:
├── CPU: Dual-core
├── RAM: 4GB
└── Disk: 20GB free space
```

### **Software:**
```markdown
├── Ubuntu 20.04/22.04 LTS (hoặc VM)
├── Python 3.8+
├── GCC/G++ 9+
├── Git
└── Internet connection (cho cài đặt)
```

---

## 🔧 **2. CÀI ĐẶT MÔI TRƯỜNG CƠ BẢN**

### **Bước 1: Update hệ thống**
```bash
# Update package list
sudo apt update && sudo apt upgrade -y

# Kiểm tra phiên bản Ubuntu
lsb_release -a
# Expected: Ubuntu 20.04 or 22.04
```

### **Bước 2: Cài đặt Build Tools**
```bash
# Cài đặt essential build tools
sudo apt install -y \
    build-essential \
    gcc g++ \
    python3 python3-pip \
    python3-dev \
    git \
    cmake \
    ccache \
    gdb \
    valgrind

# Kiểm tra cài đặt
gcc --version        # Expected: gcc 9.x or higher
python3 --version    # Expected: Python 3.8+
git --version        # Expected: git 2.x+
```

### **Bước 3: Cài đặt Python Dependencies**
```bash
# Install pip packages
pip3 install --upgrade pip

# Install analysis tools
pip3 install \
    numpy \
    pandas \
    matplotlib \
    scipy \
    seaborn \
    jupyterlab

# Verify installation
python3 -c "import numpy, pandas, matplotlib; print('✅ Python packages OK')"
```

---

## 🌐 **3. CÀI ĐẶT NS-3 SIMULATOR**

### **Method 1: Cài đặt từ Source (Khuyến nghị)**

#### **Bước 1: Download NS-3**
```bash
# Tạo thư mục làm việc
mkdir -p ~/tcp-thesis
cd ~/tcp-thesis

# Download NS-3 3.39 (phiên bản ổn định)
wget https://www.nsnam.org/releases/ns-allinone-3.39.tar.bz2

# Giải nén
tar xjf ns-allinone-3.39.tar.bz2
cd ns-allinone-3.39

# Kiểm tra cấu trúc
ls -la
# Sẽ thấy: ns-3.39/, netanim-3.109/, pybindgen-0.22.1/
```

#### **Bước 2: Cài đặt NS-3 Dependencies**
```bash
# NS-3 required packages
sudo apt install -y \
    g++ \
    python3-setuptools \
    qt5-default \
    mercurial \
    python3-pygraphviz \
    python3-kiwi \
    python3-pygoocanvas \
    libgoocanvas-dev \
    ipython3 \
    tcpdump \
    wireshark \
    autoconf \
    cvs \
    bzr \
    unrar \
    gsl-bin \
    libgsl-dev \
    libgslcblas0 \
    flex \
    bison \
    libfl-dev \
    sqlite \
    sqlite3 \
    libsqlite3-dev \
    libxml2 \
    libxml2-dev \
    libgtk-3-dev \
    vtun \
    lxc \
    uncrustify \
    doxygen \
    graphviz \
    imagemagick \
    python3-sphinx \
    dia \
    libbz2-dev \
    libssl-dev

# Optional: để visualize với NetAnim
sudo apt install -y \
    qt5-default \
    qttools5-dev-tools \
    qtmultimedia5-dev
```

#### **Bước 3: Build NS-3**
```bash
cd ~/tcp-thesis/ns-allinone-3.39

# Build tất cả (mất 20-40 phút)
./build.py --enable-examples --enable-tests

# Hoặc build chỉ NS-3 (nhanh hơn)
cd ns-3.39

# Configure NS-3
./ns3 configure --enable-examples --enable-tests

# Build
./ns3 build

# Kiểm tra build thành công
./test.py

# Expected output: PASS: All tests passed
```

#### **Bước 4: Verify NS-3 Installation**
```bash
cd ~/tcp-thesis/ns-allinone-3.39/ns-3.39

# Chạy example đơn giản
./ns3 run first

# Expected output:
# At time +2s client sent 1024 bytes to 10.1.1.2 port 9
# At time +2.00369s server received 1024 bytes from 10.1.1.1 port 49153
# ...

# Nếu chạy thành công -> NS-3 đã sẵn sàng! ✅
```

---

## 🦈 **4. CÀI ĐẶT WIRESHARK VÀ CÁC CÔNG CỤ PHÂN TÍCH**

### **Bước 1: Cài đặt Wireshark**
```bash
# Install Wireshark
sudo apt install -y wireshark

# Cho phép non-root user capture packets
sudo dpkg-reconfigure wireshark-common
# Chọn "Yes" khi được hỏi

# Thêm user vào wireshark group
sudo usermod -aG wireshark $USER

# Reboot để apply changes
sudo reboot

# Sau khi reboot, verify
wireshark --version
# Expected: Wireshark 3.x or higher
```

### **Bước 2: Cài đặt tshark (Command-line Wireshark)**
```bash
# tshark đi kèm với wireshark
tshark --version

# Test capture
sudo tshark -i lo -c 10
# Ctrl+C để dừng
```

### **Bước 3: Cài đặt tcpdump**
```bash
# Install tcpdump
sudo apt install -y tcpdump

# Test
sudo tcpdump -i lo -c 5

# Verify
tcpdump --version
```

### **Bước 4: Cài đặt iperf (Bandwidth testing)**
```bash
# Install iperf3
sudo apt install -y iperf3

# Verify
iperf3 --version
```

### **Bước 5: Cài đặt nmap**
```bash
# Install nmap
sudo apt install -y nmap

# Test
nmap --version
```

---

## 📦 **5. SETUP REPOSITORY**

### **Bước 1: Clone Repository**
```bash
cd ~/tcp-thesis

# Clone repo
git clone https://github.com/Sudhir878786/Computer_Networks-CS301.git

# Rename để dễ làm việc
mv Computer_Networks-CS301 CN-Assignment

cd CN-Assignment
ls -la

# Sẽ thấy:
# Assignment 1/
# Assignment 2/  <-- Chúng ta sẽ làm việc ở đây
```

### **Bước 2: Explore Assignment 2**
```bash
cd "Assignment 2"
ls -la

# Cấu trúc (có thể khác):
# TCP_Demo.cc        - NS-3 simulation script
# Readme.md          - Instructions
# analysis.py        - Python analysis script
# plots/             - Output plots directory
```

### **Bước 3: Copy NS-3 Scripts**
```bash
# Copy TCP_Demo.cc vào NS-3
cp TCP_Demo.cc ~/tcp-thesis/ns-allinone-3.39/ns-3.39/scratch/

# Verify
ls ~/tcp-thesis/ns-allinone-3.39/ns-3.39/scratch/
# Should see: TCP_Demo.cc
```

---

## 🎯 **6. CHẠY THÍ NGHIỆM ĐẦU TIÊN**

### **Experiment 1: Basic TCP NewReno vs CUBIC**

#### **Bước 1: Prepare Script**
```bash
cd ~/tcp-thesis/ns-allinone-3.39/ns-3.39

# Mở TCP_Demo.cc để xem
cat scratch/TCP_Demo.cc | head -50
```

#### **Bước 2: Modify Script (nếu cần)**
```cpp
// scratch/TCP_Demo.cc
// Tìm dòng này:
Config::SetDefault("ns3::TcpL4Protocol::SocketType", 
                   TypeIdValue(TcpNewReno::GetTypeId()));

// Để chạy CUBIC, đổi thành:
Config::SetDefault("ns3::TcpL4Protocol::SocketType", 
                   TypeIdValue(TcpCubic::GetTypeId()));
```

#### **Bước 3: Build và Run**
```bash
cd ~/tcp-thesis/ns-allinone-3.39/ns-3.39

# Build
./ns3 build

# Run với TCP NewReno
./ns3 run "scratch/TCP_Demo --tcpVariant=TcpNewReno --runtime=60"

# Expected output:
# Creating Topology...
# Starting Simulation...
# Simulation finished
# Results saved to: tcp-newreno-cwnd.dat

# Check output files
ls *.dat *.pcap
```

#### **Bước 4: Run với TCP CUBIC**
```bash
# Run với CUBIC để so sánh
./ns3 run "scratch/TCP_Demo --tcpVariant=TcpCubic --runtime=60"

# Kết quả: tcp-cubic-cwnd.dat
```

---

## 📊 **7. PHÂN TÍCH DỮ LIỆU**

### **Method 1: Analyze với Python**

#### **Script phân tích cơ bản:**
```python
# analyze_tcp.py
"""
TCP Congestion Window Analysis
"""
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

def load_cwnd_data(filename):
    """Load congestion window data"""
    data = pd.read_csv(filename, sep='\t', 
                       names=['Time', 'CongestionWindow'])
    return data

def plot_cwnd(newreno_file, cubic_file):
    """Compare TCP variants"""
    # Load data
    newreno = load_cwnd_data(newreno_file)
    cubic = load_cwnd_data(cubic_file)
    
    # Plot
    plt.figure(figsize=(12, 6))
    plt.plot(newreno['Time'], newreno['CongestionWindow'], 
             label='TCP NewReno', linewidth=2)
    plt.plot(cubic['Time'], cubic['CongestionWindow'], 
             label='TCP CUBIC', linewidth=2, alpha=0.8)
    
    plt.xlabel('Time (seconds)', fontsize=12)
    plt.ylabel('Congestion Window (bytes)', fontsize=12)
    plt.title('TCP Congestion Window Comparison', fontsize=14)
    plt.legend()
    plt.grid(True, alpha=0.3)
    plt.tight_layout()
    plt.savefig('cwnd_comparison.png', dpi=300)
    print("✅ Plot saved: cwnd_comparison.png")

def calculate_metrics(filename):
    """Calculate performance metrics"""
    data = load_cwnd_data(filename)
    
    metrics = {
        'Average CWND': data['CongestionWindow'].mean(),
        'Max CWND': data['CongestionWindow'].max(),
        'Min CWND': data['CongestionWindow'].min(),
        'Std Dev': data['CongestionWindow'].std(),
        'Total Time': data['Time'].max()
    }
    
    return metrics

if __name__ == '__main__':
    # Files
    newreno_file = 'tcp-newreno-cwnd.dat'
    cubic_file = 'tcp-cubic-cwnd.dat'
    
    # Plot comparison
    plot_cwnd(newreno_file, cubic_file)
    
    # Calculate metrics
    print("\n📊 TCP NewReno Metrics:")
    for k, v in calculate_metrics(newreno_file).items():
        print(f"  {k}: {v:.2f}")
    
    print("\n📊 TCP CUBIC Metrics:")
    for k, v in calculate_metrics(cubic_file).items():
        print(f"  {k}: {v:.2f}")
```

#### **Chạy script:**
```bash
cd ~/tcp-thesis/ns-allinone-3.39/ns-3.39

# Copy script vào
nano analyze_tcp.py
# Paste code ở trên, save (Ctrl+X, Y, Enter)

# Run analysis
python3 analyze_tcp.py

# Xem kết quả
display cwnd_comparison.png
```

---

### **Method 2: Analyze với Wireshark**

#### **Bước 1: Generate PCAP files**
```cpp
// Thêm vào TCP_Demo.cc (nếu chưa có):
PointToPointHelper pointToPoint;
pointToPoint.SetDeviceAttribute("DataRate", StringValue("10Mbps"));
pointToPoint.SetChannelAttribute("Delay", StringValue("10ms"));

// Enable PCAP tracing
pointToPoint.EnablePcapAll("tcp-demo");
```

#### **Bước 2: Run và capture**
```bash
# Run simulation
./ns3 run scratch/TCP_Demo

# List PCAP files
ls *.pcap
# Expected: tcp-demo-0-0.pcap, tcp-demo-1-0.pcap, etc.
```

#### **Bước 3: Analyze với Wireshark GUI**
```bash
# Open in Wireshark
wireshark tcp-demo-0-0.pcap &
```

#### **Trong Wireshark:**
```markdown
📊 WIRESHARK ANALYSIS STEPS:

1. Filter TCP traffic:
   - Filter: tcp
   - Apply

2. View Congestion Window:
   - Statistics → TCP Stream Graphs → Time-Sequence (Stevens)
   - Chọn stream 0
   
3. View Throughput:
   - Statistics → TCP Stream Graphs → Throughput
   
4. View RTT:
   - Statistics → TCP Stream Graphs → Round Trip Time
   
5. Export data:
   - File → Export Packet Dissections → As CSV
```

#### **Bước 4: Analyze với tshark (CLI)**
```bash
# Extract congestion window
tshark -r tcp-demo-0-0.pcap -T fields \
       -e frame.time_relative \
       -e tcp.analysis.bytes_in_flight \
       -E header=y \
       -E separator=, > tcp_cwnd.csv

# Extract RTT
tshark -r tcp-demo-0-0.pcap -T fields \
       -e frame.time_relative \
       -e tcp.analysis.ack_rtt \
       -E header=y \
       -E separator=, > tcp_rtt.csv

# View summary
tshark -r tcp-demo-0-0.pcap -q -z io,stat,1
```

---

## 📈 **8. ADVANCED ANALYSIS SCRIPTS**

### **Script 1: Comprehensive TCP Analysis**
```python
# comprehensive_tcp_analysis.py
"""
Comprehensive TCP Performance Analysis
"""
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
from scipy import stats

class TCPAnalyzer:
    def __init__(self, cwnd_file, pcap_csv=None):
        self.cwnd_data = self.load_cwnd(cwnd_file)
        if pcap_csv:
            self.pcap_data = pd.read_csv(pcap_csv)
    
    def load_cwnd(self, filename):
        return pd.read_csv(filename, sep='\t', 
                          names=['Time', 'CWND'])
    
    def calculate_throughput(self, window_size=1.0):
        """Calculate average throughput"""
        # Throughput = CWND / RTT (simplified)
        throughput = []
        times = []
        
        for i in range(0, len(self.cwnd_data), int(window_size*1000)):
            window = self.cwnd_data.iloc[i:i+int(window_size*1000)]
            avg_cwnd = window['CWND'].mean()
            throughput.append(avg_cwnd * 8 / window_size / 1e6)  # Mbps
            times.append(window['Time'].mean())
        
        return times, throughput
    
    def plot_all_metrics(self):
        """Plot all metrics in one figure"""
        fig, axes = plt.subplots(2, 2, figsize=(15, 10))
        
        # CWND over time
        axes[0, 0].plot(self.cwnd_data['Time'], 
                       self.cwnd_data['CWND'])
        axes[0, 0].set_title('Congestion Window Over Time')
        axes[0, 0].set_xlabel('Time (s)')
        axes[0, 0].set_ylabel('CWND (bytes)')
        axes[0, 0].grid(True, alpha=0.3)
        
        # CWND distribution
        axes[0, 1].hist(self.cwnd_data['CWND'], bins=50, 
                       edgecolor='black')
        axes[0, 1].set_title('CWND Distribution')
        axes[0, 1].set_xlabel('CWND (bytes)')
        axes[0, 1].set_ylabel('Frequency')
        axes[0, 1].grid(True, alpha=0.3)
        
        # Throughput
        times, throughput = self.calculate_throughput()
        axes[1, 0].plot(times, throughput, marker='o')
        axes[1, 0].set_title('Throughput Over Time')
        axes[1, 0].set_xlabel('Time (s)')
        axes[1, 0].set_ylabel('Throughput (Mbps)')
        axes[1, 0].grid(True, alpha=0.3)
        
        # Growth rate
        cwnd_diff = np.diff(self.cwnd_data['CWND'])
        axes[1, 1].plot(cwnd_diff)
        axes[1, 1].set_title('CWND Growth Rate')
        axes[1, 1].set_xlabel('Sample')
        axes[1, 1].set_ylabel('CWND Change (bytes)')
        axes[1, 1].grid(True, alpha=0.3)
        axes[1, 1].axhline(y=0, color='r', linestyle='--', alpha=0.5)
        
        plt.tight_layout()
        plt.savefig('comprehensive_analysis.png', dpi=300)
        print("✅ Comprehensive analysis saved")
    
    def statistical_summary(self):
        """Print statistical summary"""
        print("\n" + "="*50)
        print("STATISTICAL SUMMARY")
        print("="*50)
        
        cwnd = self.cwnd_data['CWND']
        
        print(f"Mean CWND:        {cwnd.mean():.2f} bytes")
        print(f"Median CWND:      {cwnd.median():.2f} bytes")
        print(f"Std Dev:          {cwnd.std():.2f} bytes")
        print(f"Min CWND:         {cwnd.min():.2f} bytes")
        print(f"Max CWND:         {cwnd.max():.2f} bytes")
        print(f"25th Percentile:  {cwnd.quantile(0.25):.2f} bytes")
        print(f"75th Percentile:  {cwnd.quantile(0.75):.2f} bytes")
        
        # Throughput stats
        times, throughput = self.calculate_throughput()
        print(f"\nAvg Throughput:   {np.mean(throughput):.2f} Mbps")
        print(f"Max Throughput:   {np.max(throughput):.2f} Mbps")
        
        print("="*50 + "\n")

# Usage
if __name__ == '__main__':
    # Analyze NewReno
    print("Analyzing TCP NewReno...")
    analyzer_newreno = TCPAnalyzer('tcp-newreno-cwnd.dat')
    analyzer_newreno.statistical_summary()
    analyzer_newreno.plot_all_metrics()
    
    # Analyze CUBIC
    print("Analyzing TCP CUBIC...")
    analyzer_cubic = TCPAnalyzer('tcp-cubic-cwnd.dat')
    analyzer_cubic.statistical_summary()
```

---

### **Script 2: Comparison Script**
```python
# compare_tcp_variants.py
"""
Compare multiple TCP variants
"""
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

def compare_variants(files_dict):
    """
    Compare multiple TCP variants
    
    Args:
        files_dict: {'Variant Name': 'filename.dat'}
    """
    fig, axes = plt.subplots(2, 2, figsize=(15, 10))
    
    colors = ['blue', 'red', 'green', 'orange', 'purple']
    
    # Plot 1: CWND Comparison
    for idx, (name, file) in enumerate(files_dict.items()):
        data = pd.read_csv(file, sep='\t', names=['Time', 'CWND'])
        axes[0, 0].plot(data['Time'], data['CWND'], 
                       label=name, color=colors[idx], alpha=0.7)
    
    axes[0, 0].set_title('Congestion Window Comparison', fontsize=14)
    axes[0, 0].set_xlabel('Time (s)')
    axes[0, 0].set_ylabel('CWND (bytes)')
    axes[0, 0].legend()
    axes[0, 0].grid(True, alpha=0.3)
    
    # Plot 2: Average CWND Bar Chart
    avg_cwnds = []
    names = []
    for name, file in files_dict.items():
        data = pd.read_csv(file, sep='\t', names=['Time', 'CWND'])
        avg_cwnds.append(data['CWND'].mean())
        names.append(name)
    
    axes[0, 1].bar(names, avg_cwnds, color=colors[:len(names)])
    axes[0, 1].set_title('Average Congestion Window', fontsize=14)
    axes[0, 1].set_ylabel('Average CWND (bytes)')
    axes[0, 1].grid(True, alpha=0.3, axis='y')
    
    # Plot 3: Max CWND Comparison
    max_cwnds = []
    for name, file in files_dict.items():
        data = pd.read_csv(file, sep='\t', names=['Time', 'CWND'])
        max_cwnds.append(data['CWND'].max())
    
    axes[1, 0].bar(names, max_cwnds, color=colors[:len(names)])
    axes[1, 0].set_title('Maximum Congestion Window', fontsize=14)
    axes[1, 0].set_ylabel('Max CWND (bytes)')
    axes[1, 0].grid(True, alpha=0.3, axis='y')
    
    # Plot 4: Std Dev Comparison
    std_devs = []
    for name, file in files_dict.items():
        data = pd.read_csv(file, sep='\t', names=['Time', 'CWND'])
        std_devs.append(data['CWND'].std())
    
    axes[1, 1].bar(names, std_devs, color=colors[:len(names)])
    axes[1, 1].set_title('CWND Standard Deviation', fontsize=14)
    axes[1, 1].set_ylabel('Std Dev (bytes)')
    axes[1, 1].grid(True, alpha=0.3, axis='y')
    
    plt.tight_layout()
    plt.savefig('tcp_variants_comparison.png', dpi=300)
    print("✅ Comparison plot saved: tcp_variants_comparison.png")
    
    # Print summary table
    print("\n" + "="*70)
    print(f"{'TCP Variant':<20} {'Avg CWND':<15} {'Max CWND':<15} {'Std Dev':<15}")
    print("="*70)
    for idx, name in enumerate(names):
        print(f"{name:<20} {avg_cwnds[idx]:<15.2f} {max_cwnds[idx]:<15.2f} {std_devs[idx]:<15.2f}")
    print("="*70 + "\n")

# Usage
if __name__ == '__main__':
    variants = {
        'TCP NewReno': 'tcp-newreno-cwnd.dat',
        'TCP CUBIC': 'tcp-cubic-cwnd.dat',
        # Add more variants here
        # 'TCP BBR': 'tcp-bbr-cwnd.dat',
    }
    
    compare_variants(variants)
```

---

## 🔥 **9. COMPLETE WORKFLOW SCRIPT**

```bash
#!/bin/bash
# run_tcp_experiments.sh
# Complete automated workflow

echo "🚀 Starting TCP Performance Analysis Experiments"
echo "================================================"

# Configuration
NS3_DIR=~/tcp-thesis/ns-allinone-3.39/ns-3.39
RESULTS_DIR=~/tcp-thesis/results
SCRIPT_NAME=TCP_Demo

# Create results directory
mkdir -p $RESULTS_DIR/{data,plots,logs}

cd $NS3_DIR

# Experiment parameters
VARIANTS=("TcpNewReno" "TcpCubic")
RUNTIMES=(30 60 120)
BANDWIDTHS=("10Mbps" "100Mbps" "1Gbps")
DELAYS=("10ms" "50ms" "100ms")

# Run experiments
for variant in "${VARIANTS[@]}"; do
    for runtime in "${RUNTIMES[@]}"; do
        for bw in "${BANDWIDTHS[@]}"; do
            for delay in "${DELAYS[@]}"; do
                
                exp_name="${variant}_${runtime}s_${bw}_${delay}"
                echo ""
                echo "📊 Running experiment: $exp_name"
                
                # Run simulation
                ./ns3 run "scratch/$SCRIPT_NAME \
                    --tcpVariant=$variant \
                    --runtime=$runtime \
                    --bandwidth=$bw \
                    --delay=$delay" \
                    > $RESULTS_DIR/logs/${exp_name}.log 2>&1
                
                # Move output files
                mv *.dat $RESULTS_DIR/data/ 2>/dev/null
                mv *.pcap $RESULTS_DIR/data/ 2>/dev/null
                
                echo "✅ Completed: $exp_name"
            done
        done
    done
done

echo ""
echo "📈 Running analysis..."
cd $RESULTS_DIR

# Run analysis
python3 analyze_tcp.py
python3 compare_tcp_variants.py

echo ""
echo "✅ All experiments completed!"
echo "📂 Results saved in: $RESULTS_DIR"
echo "================================================"
```

**Chạy workflow:**
```bash
# Tạo script
nano ~/tcp-thesis/run_tcp_experiments.sh

# Paste code trên, save

# Make executable
chmod +x ~/tcp-thesis/run_tcp_experiments.sh

# Run
~/tcp-thesis/run_tcp_experiments.sh
```

---

## 🐛 **10. TROUBLESHOOTING**

### **Problem 1: NS-3 build fails**
```bash
# Solution: Install missing dependencies
sudo apt install -y python3-dev libxml2-dev libssl-dev

# Clean and rebuild
./ns3 clean
./ns3 configure --enable-examples --enable-tests
./ns3 build
```

### **Problem 2: Wireshark can't capture packets**
```bash
# Solution: Fix permissions
sudo dpkg-reconfigure wireshark-common
sudo usermod -aG wireshark $USER
sudo reboot
```

### **Problem 3: Python import errors**
```bash
# Solution: Reinstall packages
pip3 install --upgrade --force-reinstall numpy pandas matplotlib
```

### **Problem 4: tcpdump permission denied**
```bash
# Solution: Use sudo or fix permissions
sudo setcap cap_net_raw,cap_net_admin=eip /usr/bin/tcpdump
```

### **Problem 5: NS-3 script not found**
```bash
# Solution: Check script location
ls scratch/TCP_Demo.cc

# If not found, copy again
cp /path/to/TCP_Demo.cc scratch/
```

---

## 📚 **11. TÀI LIỆU THAM KHẢO**

```markdown
📖 DOCUMENTATION:

1. NS-3 Documentation:
   └─ https://www.nsnam.org/documentation/

2. NS-3 Tutorial:
   └─ https://www.nsnam.org/docs/tutorial/html/

3. Wireshark User Guide:
   └─ https://www.wireshark.org/docs/wsug_html_chunked/

4. TCP RFC Documents:
   ├─ RFC 793: TCP Specification
   ├─ RFC 2581: TCP Congestion Control
   ├─ RFC 5681: TCP Congestion Control (updated)
   └─ RFC 8312: CUBIC for Fast Long-Distance Networks

5. Papers:
   ├─ TCP NewReno: RFC 6582
   └─ TCP CUBIC: ACM SIGOPS 2008
```

---

## ✅ **12. CHECKLIST HOÀN THÀNH**

```markdown
☐ Ubuntu 20.04/22.04 installed
☐ Build tools installed (gcc, g++, python3)
☐ NS-3 3.39 downloaded and built successfully
☐ Wireshark installed and configured
☐ tshark, tcpdump, iperf3, nmap installed
☐ Repository cloned
☐ TCP_Demo.cc copied to NS-3 scratch/
☐ First simulation runs successfully
☐ Python analysis scripts working
☐ Wireshark can open PCAP files
☐ Results directory created
☐ All experiments completed
☐ Analysis plots generated
```

---

## 🎓 **13. NEXT STEPS**

```markdown
📋 AFTER SETUP:

1. Week 1-2: Hiểu code TCP_Demo.cc
   - Đọc code
   - Modify parameters
   - Run experiments

2. Week 3-4: Collect data
   - Run với nhiều scenarios
   - Different bandwidths, delays, loss rates
   - Collect metrics

3. Week 5-6: Analysis
   - Plot graphs
   - Calculate statistics
   - Compare variants

4. Week 7-8: Writing
   - Write thesis chapters
   - Prepare slides
   - Make demo video
```

---

## 🚀 **QUICK START SUMMARY**

```bash
# === ONE-COMMAND INSTALL (Ubuntu 20.04/22.04) ===

# 1. Install everything
sudo apt update && sudo apt install -y build-essential gcc g++ python3 python3-pip git cmake wireshark tshark tcpdump iperf3 nmap && pip3 install numpy pandas matplotlib scipy seaborn

# 2. Download and build NS-3
cd ~ && mkdir tcp-thesis && cd tcp-thesis && wget https://www.nsnam.org/releases/ns-allinone-3.39.tar.bz2 && tar xjf ns-allinone-3.39.tar.bz2 && cd ns-allinone-3.39/ns-3.39 && ./ns3 configure --enable-examples --enable-tests && ./ns3 build

# 3. Clone repo
cd ~/tcp-thesis && git clone https://github.com/Sudhir878786/Computer_Networks-CS301.git && cp "Computer_Networks-CS301/Assignment 2/TCP_Demo.cc" ns-allinone-3.39/ns-3.39/scratch/

# 4. Run first experiment
cd ~/tcp-thesis/ns-allinone-3.39/ns-3.39 && ./ns3 run scratch/TCP_Demo

# ✅ DONE! Now start analyzing.
```

---

Bạn có vấn đề gì trong quá trình setup không? Tôi sẽ giúp bạn debug! 🔧
