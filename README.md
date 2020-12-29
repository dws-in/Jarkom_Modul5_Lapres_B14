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
   
11. Mendrop semua akses SSH ke  ip DMZ dari luar Topologi. <br>
   - Buat file baru dengan nama `no2.sh` untuk menyimpan script. <br>
   - Tambahkan pada file tersebut script di bawah ini. <br>
   ```
   iptables -A FORWARD -p tcp --dport 22 -d 10.151.83.120/29 -i eth0 -j DROP
   ```
   - Jalankan `bash no2.sh` untuk mengaktifkan iptables tsb. <br>
   - Untuk melihat hasilnya, lakuklan `nc -l -p 22` pada MALANG dan `nc 10.151.83.122 22` pada BIMA. Jika tidak diterima oleh MALANG maka berhasil. <br>
   
3. Membatasi DHCP dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari mana saja menggunakan iptables pada masing masing server, selebihnya akan di DROP. <br>
4. Akses ke MALANG yang berasal dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat. <br>
5. Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya. Selain itu akan diREJECT. <br>
6. SURABAYA disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada PROBOLINGGO port 80 dan MADIUN port 80. <br>
7. Semua paket didrop oleh firewall (dalam topologi) tercatat dalam log pada setiap UML yang memiliki aturan drop. <br>


