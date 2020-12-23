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
1. Membuat file `topologi.sh` sebagai berikut. <br>
   - Ketentuan untuk setia server diberi memori 128M sedangkan client dan router 96M. <br>
     ![alt text](/img/1.1.png) <br>
     
2. Mengedit file `/etc/sysctl.conf`sebagai berikut. <br>
   - Menghapus tanda pagar `#` pada baris `net.ipv4.ip_forward=1`. <br>
   - Memperbarui setting dengan menjalankan `sysctl -p`. <br>
   - Lakukan hal yang sama pada setiap uml lainnya. <br>
   
3. Mengkonfigurasi file `/etc/network/interfaces`sebagai berikut. <br>
   - Konfigurasi SURABAYA sebagai berikut. <br>
     ![alt text](/img/3.1.png) <br>
   - Konfigurasi MALANG sebagai berikut. <br>
     ![alt text](/img/3.2.png) <br>
   - Konfigurasi MOJOKERTO sebagai berikut. <br>
     ![alt text](/img/3.3.png) <br>
   - Konfigurasi PROBOLINGGO sebagai berikut. <br>
     ![alt text](/img/3.4.png) <br>
   - Konfigurasi MADIUN sebagai berikut. <br>
     ![alt text](/img/3.5.png) <br>
   - Konfigurasi GRESIK sebagai berikut. <br>
     ![alt text](/img/3.6.png) <br>
   - Konfigurasi SIDOARJO sebagai berikut. <br>
     ![alt text](/img/3.7.png) <br>
   - Jalankan `service networking restart` untuk memperbarui setting. <br>
     
4. Membuat file `routing.sh` sebagai berikut. <br>
   - 



