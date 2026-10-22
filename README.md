Jika layar *stuck* di tulisan `Load` seperti itu, artinya **format partisi atau tabel MBR pada image MikroTik v7 tidak cocok dibaca langsung oleh BIOS SeaBIOS DigitalOcean** menggunakan metode *dd* mentah dari Ubuntu. Masalah ini sangat umum terjadi pada RouterOS v7 karena perbedaan struktur *bootloader*-nya.

Untuk mengatasinya dengan cepat dan pasti tembus, kita bisa menggunakan **metode instalasi via Recovery ISO / Linux sementara di RAM**, atau cara paling praktis: **Rebuild Droplet ke Ubuntu lalu pakai script installer otomatis yang membersihkan partisi dari nol**.

Silakan ikuti langkah-langkah berikut:

1. **Rebuild Droplet ke Ubuntu 22.04:**
* Masuk ke dashboard DigitalOcean, pilih menu **Destroy** -> **Rebuild** ke **Ubuntu 22.04 LTS**.




2. Buka kembali konsol web / SSH Ubuntu Anda, lalu jalankan script pembersih total berikut agar partisi bersih dari awal:
```bash
sudo su
apt update && apt install wget unzip -y
wget https://download.mikrotik.com/routeros/7.14.3/chr-7.14.3.img.zip
unzip chr-7.14.3.img.zip

```


3. Agar proses *booting* MikroTik v7 tidak tersangkut di BIOS, tulis image menggunakan *bs=512* alih-alih 4M agar struktur bloknya terbaca sempurna oleh BIOS Droplet:
```bash
echo u > /proc/sysrq-trigger
dd if=chr-7.14.3.img of=/dev/vda bs=512 conv=fdatasync
echo b > /proc/sysrq-trigger

```



Setelah perintah `echo b` dieksekusi, server akan langsung *reboot* dan tulisan `Load` akan sukses berlanjut masuk ke menu login MikroTik RouterOS v7 tanpa *stuck*!


/ip address add address=143.198.85.243/20 interface=ether1
/ip route add gateway=143.198.80.1
