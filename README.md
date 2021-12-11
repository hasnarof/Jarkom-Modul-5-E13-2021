# E13 Soal Shift 5

Kelompok E13

|NRP           |Nama                   |
|:------------:|:---------------------:|
|05111940000090|Ihsannur Rahman Qalbi|
|05111940000003|Fairuz Hasna Rofifah|
|05111940000164|Ahmad Aunul Ma`bud|

## A. Topologi dan Pembagian Subnet

![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled.png)

![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%201.png)

## B. Pembagian IP

### Pohon Pembagian IP

![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled.jpeg)

[Tabel Pembagian IP](https://www.notion.so/b3d1938b0cd74d3ebfa96e82533b8a7b)

## C. Config Network & Routing

### Network Configuration

Foosha (Router)

```jsx
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.206.0.1
	netmask 255.255.255.252

auto eth2
iface eth2 inet static
	address 192.206.0.5
	netmask 255.255.255.252
```

Guanhao (Router)

```jsx
auto eth0
iface eth0 inet static
	address 192.206.0.2
	netmask 255.255.255.252
	gateway 192.206.0.1
  
auto eth1
iface eth1 inet static
	address 192.206.2.1
	netmask 255.255.254.0
  
auto eth2
iface eth2 inet static
	address 192.206.0.17
	netmask 255.255.255.248
  
auto eth3
iface eth3 inet static
	address 192.206.1.1
	netmask 255.255.255.0
```

Elena (300 Host)

```jsx
auto eth0
iface eth0 inet dhcp
```

Fokurou (200 Host)

```jsx
auto eth0
iface eth0 inet dhcp
```

Jorge (Web Server)

```jsx
auto eth0
iface eth0 inet static
	address 192.206.0.18
	netmask 255.255.255.248
	gateway 192.206.0.17
```

Maingate (Web Server)

```jsx
auto eth0
iface eth0 inet static
	address 192.206.0.19
	netmask 255.255.255.248
	gateway 192.206.0.17
```

Water7 (Router)

```jsx
auto eth0
iface eth0 inet static
	address 192.206.0.6
	netmask 255.255.255.252
	gateway 192.206.0.5
  
auto eth1
iface eth1 inet static
	address 192.206.4.1
	netmask 255.255.252.0
  
auto eth2
iface eth2 inet static
	address 192.206.0.9
	netmask 255.255.255.248
  
auto eth3
iface eth3 inet static
	address 192.206.0.129
	netmask 255.255.255.128
```

Cipher (700 Host)

```jsx
auto eth0
iface eth0 inet dhcp
```

Blueno (100 Host)

```jsx
auto eth0
iface eth0 inet dhcp
```

Jipangu (DHCP Server)

```jsx
auto eth0
iface eth0 inet static
	address 192.206.0.10
	netmask 255.255.255.248
  gateway 192.206.0.9
```

Doriki (DNS Server)

```jsx
auto eth0
iface eth0 inet static
	address 192.206.0.11
	netmask 255.255.255.248
  gateway 192.206.0.9
```

### Routing

```jsx
# FOOSHA
route add -net 192.206.1.0 netmask 255.255.255.0 gw 192.206.0.2
route add -net 192.206.2.0 netmask 255.255.254.0 gw 192.206.0.2
route add -net 192.206.0.16 netmask 255.255.255.248 gw 192.206.0.2
route add -net 192.206.4.0 netmask 255.255.252.0 gw 192.206.0.6
route add -net 192.206.0.128 netmask 255.255.255.128 gw 192.206.0.6
route add -net 192.206.0.8 netmask 255.255.255.248 gw 192.206.0.6

# WATER7
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.206.0.5

# GUANHAO
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.206.0.1
```

Agar routing berhasil dilakukan konfigurasi pada `sysctl.conf` pada semua router:

1. Lakukan `nano /etc/sysctl.conf`
2. Tambahkan:
    
    ```powershell
    net.ipv4.ip_forward=1
    net.ipv4.conf.all.accept_source_route = 1
    ```
    
3. Lakukan `sysctl -p` untuk mengecek konfigurasi

## D. Membuat DHCP Server & DHCP Relay

### Konfigurasi DHCP Server

Jipangu (DHCP Server)

1. Install DHCP Server
    
    ```powershell
    # Install DHCP Server
    apt-get update
    apt-get install isc-dhcp-server -y
    dhcpd --version
    
    # install nano
    apt-get install nano
    ```
    
2. Menentukan interface yang diberi layanan DHCP
    
    ```powershell
    # Buka
    nano /etc/default/isc-dhcp-server
    # Edit
    INTERFACES="eth0"
    # eth0 -> eth yang diberi layanan DHCP realtif terhadap DHCP server
    ```
    
3. Melakukan konfigurasi pada `isc-dhcp-server`
    
    ```powershell
    nano /etc/dhcp/dhcpd.conf
    ```
    
    Tambah config DHCP untuk semua netmask host (CLUENO, ELENA, CIPHER, FUKUROU)
    
    ```powershell
    # Fokurou (A3) - 200 Host
    subnet 192.206.1.0 netmask 255.255.255.0 {
      range 192.206.1.2 192.206.1.254;
      option routers 192.206.1.1;
      option broadcast-address 192.206.1.255; # IP (gateway^) || Netmask'
      option domain-name-servers 192.206.0.11;
      default-lease-time 600;
      max-lease-time 7200;
    }
    # Elena (A4) - 300 Host
    subnet 192.206.2.0 netmask 255.255.254.0 {
      range 192.206.2.2 192.206.3.254;
      option routers 192.206.2.1;
      option broadcast-address 192.206.3.255;
      option domain-name-servers 192.206.0.11;
      default-lease-time 600;
      max-lease-time 7200;
    }
    # Blueno (A5) - 100 Host
    subnet 192.206.0.128 netmask 255.255.255.128 {
      range 192.206.0.130 192.206.0.254;
      option routers 192.206.0.129;
      option broadcast-address 192.206.0.255;
      option domain-name-servers 192.206.0.11;
      default-lease-time 600;
      max-lease-time 7200;
    }
    # Cipher (A6) - 700 Host
    subnet 192.206.4.0 netmask 255.255.252.0 {
      range 192.206.4.2 192.206.7.254;
      option routers 192.206.4.1;
      option broadcast-address 192.206.7.255;
      option domain-name-servers 192.206.0.11;
      default-lease-time 600;
      max-lease-time 7200;
    }
    # DHCP Server
    subnet 192.206.0.8 netmask 255.255.255.248 {
      option routers 192.206.0.9; # relay gateway A7
    }
    ```
    
4. Restart DHCP Server
    
    ```powershell
    service isc-dhcp-server restart
    service isc-dhcp-server status
    ```
    

### **Konfigurasi DHCP Relay**

Water7, Foosha, Guanhao (DHCP Relay)

1. Install DHCP Relay
    
    ```powershell
    apt-get update
    apt-get install nano
    apt-get install isc-dhcp-relay -y
    ```
    
2. Edit `nano /etc/default/isc-dhcp-relay`
    
    ```powershell
    # Water7 -> eth0 eth1 eth2 eth3, Forward -> 192.206.0.10 (Jipangu / DHCP)
    # Foosha -> eth1 eth2, Forward -> 192.206.0.6
    # Guanhao -> eth0 eth1 eth3, Forward -> 192.206.0.1
    ```
    
3. Start DHCP Relay
    
    ```powershell
    service isc-dhcp-relay start
    ```
    

## Config DNS Forwarder

Doriki (DNS Server)

```powershell
apt-get update
apt-get install bind9 -y
apt-get install nano

nano /etc/bind/named.conf.options

# uncomment dan tambah
forwarders {
    192.168.122.1;
};

# comment
// dnssec-validation auto;

# tambahkan
allow-query{any;};

# restart bind9
service bind9 restart
```

## Soal 1: Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE

```powershell
# Pada Foosha
iptables -t nat -A POSTROUTING -s 192.206.0.0/16 -o eth0 -j SNAT --to-s 192.168.122.[xxx]
# atau
iptables -t nat -A POSTROUTING -s 192.206.0.0/16 -o eth0 -j SNAT --to source 192.168.122.[xxx]
# xxx -> ip a Foosha

# Flush (hapus rule) MASQUERADE
iptables -t nat -L
```

**Keterangan:**

- `iptables -t nat -A POSTROUTING` : menambahkan rule baru pada tabel nat pada chain POSTROUTING
- `-s 192.206.0.0/16 -o eth0 -j SNAT --to-s 192.168.122.x` : mentranslasikan asal koneksi (ip asal) dari subnet 192.206.0.0 yang keluar (-o / -out-interface) lewat eth0 diubah (/diberlakukan SNAT) menjadi 192.168.122.x (ip statis dari foosha yang disewakan NAT)

**Screenshoot**:

- Pada **Foosha:**
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%202.png)
    
- Test ping di **Blueno**:
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%203.png)
    

## Soal 2: Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.

```powershell
# Pada Foosha
iptables -A FORWARD -p tcp --dport 80 -d 192.206.0.8/29 -i eth0 -j DROP
# atau
iptables -A FORWARD -d 192.206.0.8/29 -i eth0 -p tcp --dport 80 -j DROP
```

**Keterangan**:

- `iptables -A FORWARD` : menambahkan rule pada tabel filter (karena tabelnya tidak didefinisikan), di chain FORWARD
- `-d 192.206.0.8/29 -i eth0 -p tcp --dport 80` : paket yg destinasinya ke subnet dengan NID 192.206.0.8/29 yang datang dari eth0 (foosha) dengan protocol tcp yg ingin menuju port 80 (atau HTTP)
- `-j DROP` : akan diberlakukan (-j) DROP

**Screenshoot Testing:**

- Pada Jipangu dan Doriki
    - Diinstall netcat : `apt-get install netcat`
    - Dilakukan : `nc -l -p 80`
        
        ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%204.png)
        
        ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%205.png)
        
- Pada **Foosha**
    - Dilakukan `nmap -p 80 192.206.0.10` dan `nmap -p 80 192.206.0.11`
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%206.png)
    

## Soal 3: Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

```powershell
# Pada JIPANGU dan DORIKI
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

**Keterangan:**

- `iptables -A INPUT` : menambahkan rule pada tabel filter (karena tidak didefinisikan), pada chain INPUT
- `-p icmp -m connlimit` : paket yang berasal dari protocol icmp akan diberikan rule connection limit
- `--connlimit-above 3 --connlimit-mask 0` : koneksi yang ditangkap untuk diberi rule adalah koneksi yang terhubung setelah koneksi ketiga (diatas tiga), yang melalui netmaask apapun
- `-j DROP`: akan di-drop

Screenshoot Testing:

- Pada **Doriki** dan **Jipangu**:
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%207.png)
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%208.png)
    
- Test Ping pada **Blueno**, **Cipher**, **Fokurou**, **Elena pada Jipangu (192.206.0.10):**
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%209.png)
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2010.png)
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2011.png)
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2012.png)
    

## Soal 4 : Kemudian kalian diminta untuk membatasi akses ke Doriki yang berasal dari subnet Blueno, Cipher, Elena dan Fukuro dengan beraturan sebagai berikut → Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis, selain itu di reject

```powershell
# Tambahkan rule pada Doriki
# Untuk paket dari Blueno
iptables -A INPUT -s 192.206.0.128/25 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 192.206.0.128/25 -j REJECT
# Untuk paket dari Cipher
iptables -A INPUT -s 192.206.4.0/22 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 192.206.4.0/22 -j REJECT

# waktu reject
date +%Y%m%d -s "20211211"
# waktu accept
date +%Y%m%d -s "20211208"
date +%T -s "08:00:00"
```

**Keterangan:**

- Rule (1)
    - `iptables -A INPUT` : menambahkan rule pada tabel filter (karena tidak didefinisikan), pada chain INPUT
    - `-s 192.206.0.128/25 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu` : paket yang berasal dari subnet blueno (192.206.0.128/25) pada jam 07:00-15:00 hari senin-kamis
    - `-j ACCEPT` : akan diaccept
- Rule (2):
    - `iptables -A INPUT -s 192.206.0.128/25 -j REJECT` : apabila tidak masuk rule diatas maka akan diberlakukan reject
- Untuk cipher rulenya sama hanya diubah NID-nya menjadi NID cipher (192.206.4.0/22)

**Screenshoot:**

- Pada **Doriki**
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2013.png)
    
- Test ping dari **Blueno** ke **Doriki**
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2014.png)
    
- Test ping dari **Cipher** ke **Doriki**
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2015.png)
    

## Soal 5 : Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya, selain itu di reject

```powershell
# Untuk paket dari Elena
iptables -A INPUT -s 192.206.2.0/23 -m time --timestart 07:00 --timestop 15:00 -j REJECT
# Untuk paket dari Fokurou
iptables -A INPUT -s 192.206.1.0/24 -m time --timestart 07:00 --timestop 15:00 -j REJECT

# waktu reject
date +%T -s "10:10:10"
# waktu accept
date +%T -s "05:10:10"
```

**Keterangan:**

- Karena waktu yang diperbolehkan adalah 15.01-06.59, maka untuk rule yang lebih efektif dan mudah dipahami, maka dibuat rule untuk mereject akses pada jam 07:00-15:00
- `iptables -A INPUT` : menambahkan rule pada tabel filter (karena tidak didefinisikan), pada chain INPUT
- `-s 192.206.2.0/23 -m time --timestart 07:00 --timestop 15:00` : paket yang berasal dari subnet elena (192.206.2.0/23) pada jam 07:00-15:00
- `-j REJECT` : akan direject
- Untuk fokurou rulenya sama hanya diubah NID-nya menjadi NID fokurou (192.206.1.0/24)

**Screenshoot:**

- Pada **Doriki**
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2016.png)
    
- Test ping dari **Elena** ke **Doriki**
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2017.png)
    
- Test ping dari **Fokurou** ke **Doriki**
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2018.png)
    

## Soal 6 : Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate

- Pada **Doriki**
    - buat domain yang mengarah ke suatu IP (pada kasus ini kami menggunakan 192.206.8.1) di file `/etc/bind/named.conf`
        
        ```powershell
        zone "jarkom-e13.com" {
                type master;
                file "/etc/bind/jarkom/jarkom-e13.com";
        };
        ```
        
    - Buat folder dengan nama `jarkom`
        
        ```powershell
        mkdir /etc/bind/jarkom
        ```
        
    - Copy `db.local` ke `/etc/bind/jarkom/jarkom-e13.com`
        
        ```powershell
        cp /etc/bind/db.local /etc/bind/jarkom/jarkom-e13.com
        ```
        
    - Edit file `/etc/bind/jarkom/jarkom-e13.com`
        
        ```powershell
        $TTL    604800
        @       IN      SOA     jarkom-e13.com. root.jarkom-e13.com. (
                                2021121101      ; Serial
                                 604800         ; Refresh
                                  86400         ; Retry
                                2419200         ; Expire
                                 604800 )       ; Negative Cache TTL
        ;
        @       IN      NS      jarkom-e13.com.
        @       IN      A       192.206.8.1
        ```
        
    - Restart bind9
        
        ```powershell
        service bind9 restart
        ```
        
- Pada **Guanhao**
    - Masukkan rule
        
        ```powershell
        iptables -t nat -A PREROUTING -p tcp -d 192.206.8.1 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.206.0.18:80
        
        iptables -t nat -A PREROUTING -p tcp -d 192.206.8.1 --dport 80 -j DNAT --to-destination 192.206.0.19:80
        
        iptables -t nat -A POSTROUTING -p tcp -d 192.206.0.18 --dport 80 -j SNAT --to-source 192.206.8.1:80
        
        iptables -t nat -A POSTROUTING -p tcp -d 192.206.0.19 --dport 80 -j SNAT --to-source 192.206.8.1:80
        
        # 192.206.0.18(Jorge)
        # 192.206.0.19(Maingate)
        ```
        

**Testing + Screenshoot:**

- Install netcat pada **Elena, Fokurou, Jorge, Maingate**
    
    ```powershell
    apt-get install netcat -y
    ```
    
- Pada **Jorge** dan **Maingate**, lakukan
    
    ```powershell
    nc -l -p 80
    ```
    
- Pada **Elena** dan **Fokurou**, lakukan
    
    ```powershell
    nc 192.206.8.1 80
    ```
    
- Ketikkan sesuatu pada **Elena** dan **Fokurou**
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2019.png)
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2020.png)
    
- Hasilnya akan muncul bergantian pada **Jorge** dan **Maingate**
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2021.png)
    
    ![Untitled](E13%20Soal%20Shift%205%201083fc988604453da6113e97b6688e6c/Untitled%2022.png)

# Pembagian Tugas

|Nama|Pengerjaan|
|:------------:|:---------------------:|
|Fairuz Hasna Rofifah|Topologi GNS3, Topologi CPT|
|Ahmad Aunul Ma`bud|Topologi GNS3, Pembagian Subnet GNS3|
|Ihsannur Rahman Qalbi|Routing CPT,Pembagian Subnet CPT|

## Kendala:
* Kurang teliti (typo) ketika memberikan ip network configuration sehingga harus debug cukup lama