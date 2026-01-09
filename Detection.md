# Suggested Tool
## For Capture
| Scenario          | Tool              |
| ----------------- | ----------------- |
| Server / headless | `tcpdump`         |
| Short analysis    | Wireshark         |
| High throughput   | `dumpcap`         |
| IDS sensor        | Zeek live capture |
## For Analysis
| Goal                 | Tool      |
| -------------------- | --------- |
| Packet inspection    | Wireshark |
| Behavioral detection | Zeek      |
| Beaconing / C2       | RITA      |


- Wireshark
    - ✔ GUI
    - ✔ Excellent packet dissection
    - ❌ Heavy
    - ❌ Not ideal for long captures

- tcpdump
    - ✔ Lightweight
    - ✔ CLI-friendly
    - ✔ Ideal for servers & long capture
    - ❌ No GUI

-dumpcap (Wireshark’s backend)
    - ✔ Very fast
    - ✔ Best of both worlds
    - ❌ Less known

# C2


Network Traffic
      ↓
PCAP (tcpdump / Wireshark)
      ↓
Zeek logs
      ↓
RITA analysis

```bash
sudo tcpdump -i eth0 -w traffic.pcap
sudo tcpdump -i eth0 -G 300 -w traffic_%Y%m%d%H%M.pcap

zeek readpcap traffic.pcap DSTDIR
zeek -r capture.pcapng


rita import zeek_logs mydataset
rita view mydataset
rita analyze mydataset
rita show-beacons mydataset
```



## RITA
Real Intelligence Threat Analytics [https://www.activecountermeasures.com/free-tools/rita/]

### Zeek
network secuirty monitoring
- transaction data (summarized records of application-layer transactions)
- extracted content data (files or artifacts extracted, such as executables)

Live Capture
`sudo zeek -i eth0`
✔️ Best for long-term monitoring
❌ Not good if you need packet-level forensic review

```shell
cd
wget https://github.com/activecm/rita/releases/download/v5.0.0-beta/rita-v5.0.0-beta.tar.gz
tar -xzvf rita-v5.0.0-beta.tar.gz
cd rita-v5.0.0-beta-installer
./install_rita.sh localhost

sudo wget -O /usr/local/bin/zeek https://raw.githubusercontent.com/activecm/docker-zeek/master/zeek
sudo chmod +x /usr/local/bin/zeek
zeek start

rita import --database=mydatabase --logs=~/mylogs
``
