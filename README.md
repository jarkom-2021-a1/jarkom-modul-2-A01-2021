# jarkom-modul-2-a01-2021

# Anggota kelompok A1 :
- 05111940000083 Fajar Satria
- 05111940000124 Gerry Sihaj
- 05111940000130 Adrian

### 1 EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet
Langkah - langkah :
- Buat topologi seperti di soal  
![1](https://user-images.githubusercontent.com/65168221/139434362-a10de08c-37aa-4ab3-9f8b-2780f2da6cc0.png)
- Sesuaikan setting nya untuk masing – masing node sebagai berikut :  

Foosha (Router)
```auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.169.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.169.2.1
	netmask 255.255.255.0
```

Loguetown (Client)
```auto eth0
iface eth0 inet static
	address 192.169.1.2
	netmask 255.255.255.0
	gateway 192.169.1.1
 ```

Alabasta (Client)
```auto eth0
iface eth0 inet static
	address 192.169.1.3
	netmask 255.255.255.0
	gateway 192.169.1.1
```

Enieslobby (DNS Master)
```auto eth0
iface eth0 inet static
	address 192.169.2.2
	netmask 255.255.255.0
	gateway 192.169.2.1
```

Water7 (DNS Slave)
```
auto eth0
iface eth0 inet static
	address 192.169.2.3
	netmask 255.255.255.0
	gateway 192.169.2.1
```

Skypie (Web server)
```
auto eth0
iface eth0 inet static
	address 192.169.2.4
	netmask 255.255.255.0
	gateway 192.169.2.1
```
- Pada Foosha jalankan, ```iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.169.0.0/16 ```
- Pada masing – masing node jalankan ```echo nameserver 192.168.122.1 > /etc/resolv.conf ```


### 2 Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku
Langkah - langkah :
Pada Enieslobby, jalankan :
- ```apt-get update```
- ```apt-get install bind9 -y```
- Ubah /etc/bind/named.conf.local menjadi seperti berikut :
![1](https://user-images.githubusercontent.com/65168221/139434777-89b320b5-6d78-4a61-94f7-45f5ad494c2f.png)

- Buat folder, ```mkdir /etc/bind/kaizoku```
- Copy file, ```cp /etc/bind/db.local /etc/bind/kaizoku/franky.A01.com```
- Ubah /etc/bind/kaizoku/franky.A01.com menjadi seperti berikut :
![2](https://user-images.githubusercontent.com/65168221/139434929-bf37502a-f5dd-473a-8f2d-c6e526940c57.png)

- Restart, ```service bind9 restart```
- Pada loguetown dan alabasta, tambahkan nameserver enieslobby dengan ```nano /etc/resolv.conf``` seperti berikut:
![3](https://user-images.githubusercontent.com/65168221/139435212-288f87a9-4dc1-40ea-aeb7-861702dcc58a.png)  

- Ping ```franky.A01.com``` dan ```www.franky.A01.com``` seperti berikut :  
![4](https://user-images.githubusercontent.com/65168221/139435263-92b0a346-6b98-4d22-af4a-6915e40134ae.png)


### 3 Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie
Langkah - langkah :
- Pada enieslobby edit file ```/etc/bind/kaizoku/franky.A01.com``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/139435872-76ec1ebb-02af-4633-bc8b-9498d4ea1930.png)

- Restart, ```service bind9 restart```
- Pada loguetown, ping ```super.franky.A01.com``` dan ```www.super.franky.A01.com``` seperti gambar berikut :  
![2](https://user-images.githubusercontent.com/65168221/139436013-653fef00-3e0b-45cf-8b79-6d386283509a.png)


### 4 Buat juga reverse domain untuk domain utama
Langkah - langkah :
- Pada enieslobby, ubah file ```/etc/bind/named.conf.local``` seperti gambar berikut :  
![1](https://user-images.githubusercontent.com/65168221/139436440-145ddf1b-7ce2-4660-b453-2d09c5ba8666.png)

- Copy file, ```cp /etc/bind/db.local /etc/bind/kaizoku/2.169.192.in-addr.arpa``` 
- Ubah file ```/etc/bind/kaizoku/2.169.192.in-addr.arpa``` seperti berikut :  
![2](https://user-images.githubusercontent.com/65168221/139436455-4902eb0f-1d1a-4dc7-9428-a6734d60eef7.png)

- Restart, ```service bind9 restart```
- Pada loguetown, jalankan ```host -t PTR 192.169.2.2```  
![3](https://user-images.githubusercontent.com/65168221/139436465-4032c955-7443-46ef-b70f-2433e5a97c85.png)


### 5 Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama
Langkah - langkah :
- Pada enieslobby, ubah file ```/etc/bind/named.conf.local``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/139439719-8d7ca7a5-1cd3-4fae-b0c3-c4eb59489b73.png)


- Restart, ```service bind9 restart```
Pada water7, jalankan
- ```apt-get update```
- ```apt-get install bind9 -y```
- Ubah file ```/etc/bind/named.conf.local``` seperti berikut :  
![2](https://user-images.githubusercontent.com/65168221/139439724-b828de1d-a7ad-4faf-9dd9-1b9c500ec476.png)


- Restart, ```service bind9 restart```
- Pada enieslobby, jalankan ```service bind9 stop```
- Pada loguetown, ubah ```/etc/resolv.conf``` seperti berikut :  
![3](https://user-images.githubusercontent.com/65168221/139439733-2a67e935-9435-477c-af6d-5babd08c5ab6.png)


- Ping ```franky.A01.com```  
![4](https://user-images.githubusercontent.com/65168221/139439741-541f4b0b-eef0-46fd-bbb9-7c5cf9ac3efb.png)


### 6 Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo
- Pada enieslobby, edit file ```/etc/bind/kaizoku/franky.A01.com``` seperti berikut :
 ![1](https://user-images.githubusercontent.com/65168221/139443215-8fcdc326-e02d-4122-96e3-f4ffea7fb99d.png)

- Edit ```/etc/bind/named.conf.options``` seperti berikut :
![2](https://user-images.githubusercontent.com/65168221/139443223-59ae51cc-e62b-4a41-8c32-ae3b984b8112.png)

- Edit ```/etc/bind/named.conf.local``` seperti berikut :
![3](https://user-images.githubusercontent.com/65168221/139443233-bb92c874-2cfb-4f9c-bd99-cd8fe68ff0f3.png)

- Restart, ```service bind9 restart```

Pada water7, edit ```/etc/bind/named.conf.options``` seperti berikut :
![4](https://user-images.githubusercontent.com/65168221/139443243-3bbd4b1e-6c69-4cf8-97d1-9eef3caea387.png)

- Edit ```/etc/bind/named.conf.local``` seperti berikut :
![5](https://user-images.githubusercontent.com/65168221/139443251-1630f515-9fa3-4788-af5f-f3f722586d1d.png)

- Buat folder sunnygo, ```mkdir /etc/bind/sunnygo```
- Copy file, ```cp /etc/bind/db.local /etc/bind/sunnygo/mecha.franky.A01.com```
- Edit ```/etc/bind/sunnygo/mecha.franky.A01.com``` seperti berikut :
![6](https://user-images.githubusercontent.com/65168221/139443278-e46d2015-dfee-46b8-9517-11570e122a9c.png)

- Restart, ```service bind9 restart```

- Pada loguetown, ping ```mecha.franky.A01.com``` dan ```www.mecha.franky.A01.com```
![7](https://user-images.githubusercontent.com/65168221/139443290-5733aad8-ce32-425a-894e-3d54e0400fe5.png)



### 7 Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie
- Edit ```/etc/bind/sunnygo/mecha.franky.A01.com``` seperti berikut :
![1](https://user-images.githubusercontent.com/65168221/139443838-665ce3fa-4788-4a06-8a89-8e13cd191c47.png)

- Restart, ```service bind9 restart```
- Di loguetown, ping ```general.mecha.franky.A01.com``` dan ```www.general.mecha.franky.A01.com```
![2](https://user-images.githubusercontent.com/65168221/139443848-d7f990bc-114b-4ba1-88e8-ddfff9d7fe79.png)


### 8 Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.franky.yyy.com. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada /var/www/franky.yyy.com.
- Pada enieslobby, edit ```/etc/bind/kaizoku/franky.A01.com``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/139445101-5e30ec09-c9a4-495e-ac06-67a9da5e2d9f.png)

- Edit ```/etc/bind/kaizoku/2.169.192.in-addr.arpa``` seperti di bawah ini :  
![2](https://user-images.githubusercontent.com/65168221/139445111-dd422eb3-96cf-4c05-9896-9793d4e8bc5f.png)

Pada skypie, jalankan :
- ```apt-get install apache2 -y```
- ```apt-get install php -y```
- ```apt-get install libapache2-mod-php7.0 -y```

- Pindah ke direktori, ```/etc/apache2/sites-available```
- Copy file, ```cp 000-default.conf franky.A01.com.conf```
- Edit ```franky.A01.com.conf``` seperti berikut :  
![3](https://user-images.githubusercontent.com/65168221/139445137-cd858ca0-5fe4-4b5d-9d14-23ccaccac7af.png)

- Aktifkan konfigurasi, ```a2ensite franky.A01.com```
- Restart service, ```service apache2 restart```
- Pindah ke direktori ```/var/www```
- Install wget, ```apt-get install wget```
- Download file dari link berikut dengan
```wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/franky.zip```
- Install unzip, ```apt-get install unzip```
- Unzip, ```unzip franky.zip```
- Rename folder franky menjadi franky.A01.com, ```mv franky franky.A01.com```
- Pada loguetown, install lynx, ```install apt-get install lynx -y```
- Buka web, ```lynx franky.A01.com```  
![4](https://user-images.githubusercontent.com/65168221/139445155-111851cb-2ead-4396-97b6-e043f760d8a3.png)


### 9 Setelah itu, Luffy juga membutuhkan agar url www.franky.yyy.com/index.php/home dapat menjadi menjadi www.franky.yyy.com/home
Langkah - langkah :
- Pindah ke direktori ```/etc/apache2/sites-available```
- Edit file ```franky.A01.com.conf```  
![1](https://user-images.githubusercontent.com/65168221/139445915-8408dd2a-174f-47d6-97b9-f7c6aaee0e7a.png)

- restart service, ```service apache2 restart```
- Pada loguetown, buka ```lynx www.franky.A01.com/home```  
![2](https://user-images.githubusercontent.com/65168221/139445924-9ab9bf6a-cf1a-4cd8-a1b9-024a6e860d20.png)
![3](https://user-images.githubusercontent.com/65168221/139445935-b34eee36-7186-471a-848f-ca9987f73540.png)


### 10 Setelah itu, pada subdomain www.super.franky.yyy.com, Luffy membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/super.franky.yyy.com
Langkah - langkah :
- Pada skypie, pindah ke direktori ```/etc/apache2/sites-available```
- Copy file, ```cp 000-default.conf super.franky.A01.com.conf```
- Edit file ```super.franky.A01.com.conf```  
![1](https://user-images.githubusercontent.com/65168221/139446301-eb623800-ebc9-4482-babd-6388d9e999d5.png)

- Aktifkan konfigurasi, ```a2ensite franky.A01.com```
- Restart service, ```service apache2 restart```
- Pindah ke directory, ```/var/www```
- Download file zip
```wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/super.franky.zip```
- Unzip file, ```unzip super.franky.zip```
- Rename folder super.franky menjadi super.franky.A01.com, ```mv super.franky super.franky.A01.com```
- Pada loguetown, buka ```lynx www.super.franky.A01.com```  
![2](https://user-images.githubusercontent.com/65168221/139446307-035e0e91-786e-40d5-9441-8f75e9766982.png)
![3](https://user-images.githubusercontent.com/65168221/139446312-17d03522-e207-4d51-a98a-e8230976f785.png)


### 11 Akan tetapi, pada folder /public, Luffy ingin hanya dapat melakukan directory listing saja.
Langkah - langkah :
- Menuju ke direktori, ```/etc/apache2/sites-available```
- Edit file ```super.franky.A01.com.conf```
gambar 1
![1](https://user-images.githubusercontent.com/65168221/139446628-188d259d-b59f-4571-8571-720e6309400c.png)

- Restart service, ```service apache2 restart```
- Di loguetown, buka ```lynx www.super.franky.A01.com/public```  
![2](https://user-images.githubusercontent.com/65168221/139446636-908e9271-8775-4440-b99f-7aa5a72952cb.png)
![3](https://user-images.githubusercontent.com/65168221/139446644-9b1e388a-df3e-402d-8cdf-2d29a76a11b9.png)


### 12 Tidak hanya itu, Luffy juga menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache
Langkah - langkah :
- Pindah ke direktori ```/etc/apache2/sites-available```
- Edit file ```super.franky.A01.com.conf``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/139446713-a8dde1ae-4b14-4bc6-839b-b34e78fdc50e.png)

- Restart service, ```service apache2 restart```
- Pada loguetown, coba ```lynx www.super.franky.A01.com/errorhehe```  
![2](https://user-images.githubusercontent.com/65168221/139446737-cae982e3-c4ef-43ed-834a-f5e476d0f683.png)
![3](https://user-images.githubusercontent.com/65168221/139446746-f65c4439-f0f1-46e9-abe9-ec4709f56d6d.png)


### 13 Luffy juga meminta Nami untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.super.franky.yyy.com/public/js menjadi www.super.franky.yyy.com/js
Langkah - langkah :
- Menuju direktori ```/etc/apache2/sites-available```
- Edit file ```super.franky.A01.com.conf```  
![1](https://user-images.githubusercontent.com/65168221/139447440-68f06e01-5590-4641-ae10-2a256f5808b2.png)

- Restart apache, ```service apache2 restart```
- Pada loguetown, coba ```lynx www.super.franky.A01.com/js```  
![2](https://user-images.githubusercontent.com/65168221/139447448-66e37610-916c-4f86-802a-50481d74f961.png)
![3](https://user-images.githubusercontent.com/65168221/139447455-8038f905-7d93-4806-b46e-1d9e3dc6df56.png)


### 14 Dan Luffy meminta untuk web www.general.mecha.franky.yyy.com hanya bisa diakses dengan port 15000 dan port 15500
Langkah - langkah :
- Pindah ke direktori, ```/etc/apache2/sites-available```
- Copy file, ```cp 000-default.conf general.mecha.franky.A01.com.conf```
- Edit file ```general.mecha.franky.A01.com.conf``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/139448038-d7b7a1a9-1f40-492e-9a6a-eebf39a057f8.png)

- Edit file ```/etc/apache2/ports.conf```  
![2](https://user-images.githubusercontent.com/65168221/139448055-b8de4ef4-953a-44ec-9c52-eefc07a40a1b.png)

- Jalankan ```a2ensite general.mecha.franky.A01.com```
- Restart service, ```service apache2 restart```
- Di direktori /var/www, download zip
```wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/general.mecha.franky.zip```
- Unzip file, ```unzip general.mecha.franky.zip```
- Rename folder, ```mv general.mecha.franky general.mecha.franky.A01.com```
- Pada loguetown, coba ```lynx www.general.mecha.franky.A01.com:15000``` dan ```lynx www.general.mecha.franky.A01.com:15500```  
![3](https://user-images.githubusercontent.com/65168221/139448075-cce8bdf9-91ea-40a8-8987-96ec267c5515.png)
![4](https://user-images.githubusercontent.com/65168221/139448082-e7778219-6216-4f5a-b757-a3fdaa8e3079.png)


### 15 dengan autentikasi username luffy dan password onepiece dan file di /var/www/general.mecha.franky.yyy
Langkah - langkah :
- Buka direktori, ```/etc/apache2/sites-available```
- Edit file ```general.mecha.franky.A01.com.conf```  
![1](https://user-images.githubusercontent.com/65168221/139448374-35ca7025-551d-4d5f-be46-361d75f72026.png)

- Jalankan perintah berikut untuk menambah akun, ```htpasswd -c /etc/apache2/.htpasswd luffy``` dan masukkan password nya : onepiece  
![2](https://user-images.githubusercontent.com/65168221/139448388-a912fea0-5f43-4d93-b3e9-fb4f317edbbb.png)

- Restart service, ```service apache2 restart```
- Pada loguetown, jalankan ```lynx www.general.mecha.franky.A01.com:15000```  
![3](https://user-images.githubusercontent.com/65168221/139448393-66640a21-3948-4b18-a946-a75573bbcc32.png)

- Masukkan username : luffy dan password : onepiece  
![4](https://user-images.githubusercontent.com/65168221/139448406-b392883d-4468-47eb-a0c0-d91dbd39672e.png)
![5](https://user-images.githubusercontent.com/65168221/139448408-059cbacd-6a7a-4d66-96a1-75d736ef8690.png)


### 16 Dan setiap kali mengakses IP Skypie akan dialihkan secara otomatis ke www.franky.yyy.com
- Buka direktori ```/etc/apache2/sites-available```
- Edit file, ```nano 000-default.conf```  
![1](https://user-images.githubusercontent.com/65168221/139448594-7c42ea7c-37b9-47ae-8a14-855be497b109.png)

- Restart service, ```service apache2 restart```
- Pada loguetown, jalankan ```lynx 192.169.2.4```  
![2](https://user-images.githubusercontent.com/65168221/139448606-8519d541-252f-4948-8969-c0fe78090558.png)
![3](https://user-images.githubusercontent.com/65168221/139448611-2e8416ac-2d68-44d2-8411-e3caf17a1d59.png)


### 17 Dikarenakan Franky juga ingin mengajak temannya untuk dapat menghubunginya melalui website www.super.franky.yyy.com, dan dikarenakan pengunjung web server pasti akan bingung dengan randomnya images yang ada, maka Franky juga meminta untuk mengganti request gambar yang memiliki substring “franky” akan diarahkan menuju franky.png. Maka bantulah Luffy untuk membuat konfigurasi dns dan web server ini!
- Pada skypie, jalankan ```a2enmod rewrite```
- Restart service, ```service apache2 restart```
- Pindah ke folder ```/var/www/super.franky.A01.com```
- Buat file .htaccess seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/139449011-72749d39-90c3-4b8c-ae60-f4b6a7aad06d.png)

- Pindah ke folder ```/etc/apache2/sites-available```
- Edit file ```super.franky.A01.com.conf``` seperti berikut :  
![2](https://user-images.githubusercontent.com/65168221/139449021-cfe83d3d-c300-42ab-b149-488c964db5ad.png)

- Restart service, ```service apache2 restart```
- Pada loguetown coba buka ```lynx www.super.franky.A01.com/public/images/franky.png```  
![3](https://user-images.githubusercontent.com/65168221/139449026-f238edc3-a8e9-44a8-8307-687b5261af3a.png)

- coba ```lynx www.super.franky.A01.com/public/images/car.jpg```  
![4](https://user-images.githubusercontent.com/65168221/139449039-7f84d50a-bad8-4581-9781-1de9e50f7c69.png)

- coba ```lynx www.super.franky.A01.com/public/images/papa.franky.jpg```  
![5](https://user-images.githubusercontent.com/65168221/139449048-80433d54-58c0-497c-8e08-24c2a839b9ef.png)

- coba ```lynx www.super.franky.A01.com/public/images/whatMatters.jpg```  
![6](https://user-images.githubusercontent.com/65168221/139449057-696ca146-fcd9-445b-8e20-6f07ce2dbb35.png)


Kendala : 
- a
- a
- a

Pembagian Tugas :
- a
- a
- a
