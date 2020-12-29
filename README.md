# Jarkom_Modul5_Lapres_B14 <br>
Oleh:
- Iqbaal Pratama Putra (05111840000021) <br>
- Dwi Wahyu Santoso (05111840000121) <br>

## Topologi <br>
![alt text](/img/topologi.png)<br>

**Keterangan:** <br>
- MALANG: DNS Server <br>
- MOJOKERTO: DHCP Server <br>
- MADIUN: Web Server <br>
- PROBOLINGGO: Web Server <br>
- SIDOARJO: Client 200 host <br>
- GRESIK: Client 210 host <br>

## Langkah-langkah Pengerjaan <br>
1. Melakukan perhitungan subnet berdasarkan ketentuan menggunakan VLSM<br>
   - Pembagian subnet:<br>
    ![alt text](/img/table.png) <br>
    ![alt text](/img/vlsm.png) <br>
    ![alt text](/img/tree.png) <br>
    
2. Membuat file `topologi.sh` sebagai berikut. <br>
   - Ketentuan untuk setia server diberi memori 128M sedangkan client dan router 96M. <br>
     ![alt text](/img/2.1.png) <br>
    
3. Mengedit file `/etc/sysctl.conf`sebagai berikut. <br>
   - Menghapus tanda pagar `#` pada baris `net.ipv4.ip_forward=1`. <br>
   - Memperbarui setting dengan menjalankan `sysctl -p`. <br>
   - Lakukan hal yang sama pada setiap uml lainnya. <br>

4. Mengkonfigurasi file `/etc/network/interfaces`sebagai berikut. <br>
   - Konfigurasi SURABAYA sebagai berikut. <br>
     ![alt text](/img/4.1.png) <br>
   - Konfigurasi BATU sebagai berikut. <br>
     ![alt text](/img/4.2.png) <br>
   - Konfigurasi KEDIRI sebagai berikut. <br>
     ![alt text](/img/4.3.png) <br>
   - Konfigurasi MALANG sebagai berikut. <br>
     ![alt text](/img/4.4.png) <br>
   - Konfigurasi MOJOKERTO sebagai berikut. <br>
     ![alt text](/img/4.5.png) <br>
   - Konfigurasi PROBOLINGGO sebagai berikut. <br>
     ![alt text](/img/4.6.png) <br>
   - Konfigurasi MADIUN sebagai berikut. <br>
     ![alt text](/img/4.7.png) <br>
   - Konfigurasi GRESIK sebagai berikut. <br>
     ![alt text](/img/4.8.png) <br>
   - Konfigurasi SIDOARJO sebagai berikut. <br>
     ![alt text](/img/4.9.png) <br>
   - Jalankan `service networking restart` untuk memperbarui setting. <br>

5. Membuat file `routing.sh` sebagai berikut. <br>
   - Konfigurasi routing pada SURABAYA seperti gambar di bawah ini. <br>
     ![alt text](/img/5.1.png) <br>
   - Jalankan `source routing.sh` untuk menambahkan route tersebut. <br>

6. Mengistall DHCP Server pada MOJOKERTO. <br>
   - Jalankan `apt-get update` pada MOJOKERTO. <br>
   - Lalu, jalankan `apt-get install isc-dhcp-server`. <br>
   - Edit file `/etc/default/isc-dhcp-server` pada MOJOKERTO, dengan menambahkan `INTERFACESv4="eth0". <br>
     ![alt text](/img/6.1.png) <br>
   - Selanjutnya jalankan `service isc-dhcp-server restart`. <br>
   - Edit file `/etc/dhcp/dhcp.conf`pada MOJOKERTO seperti gambar di bawah ini. <br>
     ![alt text](/img/6.2.png) <br>
     
7. Menginstall DHCP Relay pada SURABAYA. <br>
   - Jalankan `apt-get update` pada SURABAYA. <br>
   - Lalu, jalankan `apt-get install isc-dhcp-relay`. <br>
   - Edit file `/etc/default/isc-dhcp-relay` pada SURABAYA. Tambahkan `SERVERS="10.151.83.123"` dan `INTERFACES="eth1 eth2"`. <br>
     ![alt text](/img/7.1.png) <br>
     
8. Menginstall DHCP Relay pada KEDIRI. <br>
   - Jalankan `apt-get update` pada KEDIRI. <br>
   - Lalu, jalankan `apt-get install isc-dhcp-relay`. <br>
   - Edit file `/etc/default/isc-dhcp-relay` pada KEDIRI. Tambahkan `SERVERS="10.151.83.123"` dan `INTERFACES="eth0 eth1"`.  <br>
     ![alt text](/img/8.1.png) <br>
     
9. Menginstall DHCP Relay pada BATU. <br>
   - Jalankan `apt-get update` pada BATU. <br>
   - Lalu, jalankan `apt-get install isc-dhcp-relay`. <br>
   - Edit file `/etc/default/isc-dhcp-relay` pada BATU. Tambahkan `SERVERS="10.151.83.123"` dan `INTERFACES="eth0 eth1 eth2"`.  <br>
     ![alt text](/img/9.1.png) <br>
     
10. Menggunakan `iptables` pada SURABAYA untuk akses ke luar tanpa `MASQUERADE`. <br>
    - Buat file baru dengan nama `no1.sh` untuk menyimpan script. <br>
    - Tambahkan pada file tersebut script di bawah ini. <br>
    ```
    iptables -t nat -A POSTROUTING -s 192.168.0.0/22 -o eth0 -j SNAT --to-source 10.151.74.62
    ```
    - Jalankan `bash no1.sh` untuk mengaktifkan iptables tsb. <br>
    - Untuk melihat hasilnya, lakuklan `ping its.ac.id` pada setiap UML. <br> 
   
11. DROP semua akses SSH ke  ip DMZ dari luar Topologi. <br>
    - Buat file baru dengan nama `no2.sh` untuk menyimpan script. <br>
    - Tambahkan pada file tersebut script di bawah ini. <br>
    ```
    iptables -N LOGGING
    iptables -A FORWARD -p tcp --dport 22 -d 10.151.83.120/29 -i eth0 -j LOGGING
    iptables -A LOGGING -m limit --limit 5/min -j LOG --log-prefix "iptables_FORWARD_denied: " --log-level 7
    iptables -A LOGGING -j DROP
    ```
    - Jalankan `bash no2.sh` untuk mengaktifkan iptables tsb. <br>
    - Untuk melihat hasilnya, lakukan `nc -l -p 22` pada MALANG dan `nc 10.151.83.122 22` pada BIMA. Jika tidak diterima oleh MALANG maka berhasil. <br>
   
12. Membatasi DHCP dan DNS server maksimal 3 koneksi ICMP secara bersamaan, selebihnya akan di DROP. <br>
    - Buat file baru pada MALANG dan MOJOKERTO dengan nama `no3.sh` untuk menyimpan script. <br>
    - Tambahkan pada file `no3.sh` tersebut dengan script di bawah ini. <br>
    ```
    iptables -N LOGGING
    iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j LOGGING
    iptables -A LOGGING -m limit --limit 5/min -j LOG --log-prefix "iptables_FORWARD_denied: " --log-level 7
    iptables -A LOGGING -j DROP
    ```
    - Jalankan `bash no3.sh` untuk mengaktifkan iptables tsb. <br>
    - Untuk melihat hasilnya, lakukan `ping 10.151.83.122` pada 4 UML selain MALANG dan MOJOKERTO. Jika 3 UML pertama menerima ack dan yang ke-4 tidak menerima ack maka berhasil. <br>
    
13. SIDOARJO dan GRESIK diberikan waktu akses untuk mengakses server MALANG. <br>
    - SIDOARJO = 07:00 - 17:00 (Senin - Jumat) dan GRESIK = 17:00 - 07:00 (Setiap Hari). <br>
    - Buat 2 file baru pada MALANG dengan nama `no4.sh` dan `no5.sh` untuk menyimpan script. <br>
    - Tambahkan pada file `no4.sh` script di bawah ini. <br>
    ```
    iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 07:00 --timestop 16:59 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
    iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 17:00 --timestop 06:59 -j REJECT
    iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 07:00 --timestop 16:59 --weekdays Sat,Sun -j REJECT
    ```
    - Tambahkan pada file `no5.sh` script di bawah ini. <br>
    ```
    iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 07:00 --timestop 16:59 -j REJECT
    ```
    - Jalankan `bash` pada `no4.sh` dan `no5.sh` untuk mengaktifkan iptables tsb. <br>
    - Untuk melihat hasil dari script `no4.sh`, lakukan `date -s '202020-12-12 00:00:00'` pada MALANG. Kemudian dari SIDOARJO `ping 10.151.83.122` jika hasilnya unreachable maka berhasil. <br>
    - Untuk melihat hasil dari script `no5.sh`, lakukan `date -s '202020-12-12 08:00:00'` pada MALANG. Kemudian dari GRESIK `ping 10.151.83.122` jika hasilnya unreachable maka berhasil. <br>
    
4. Akses ke MALANG yang berasal dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat. <br>
5. Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya. Selain itu akan diREJECT. <br>
6. SURABAYA disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada PROBOLINGGO port 80 dan MADIUN port 80. <br>
7. Semua paket didrop oleh firewall (dalam topologi) tercatat dalam log pada setiap UML yang memiliki aturan drop. <br>


