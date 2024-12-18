madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
wget https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-12.8.0-amd64-netinst.iso
--2024-12-18 16:04:51--  https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-12.8.0-amd64-netinst.iso
Resolviendo cdimage.debian.org (cdimage.debian.org)... 2001:6b0:19::163, 2001:6b0:19::165, 194.71.11.165, ...
Conectando con cdimage.debian.org (cdimage.debian.org)[2001:6b0:19::163]:443... conectado.
Petición HTTP enviada, esperando respuesta... 302 Found
Localización: https://chuangtzu.ftp.acc.umu.se/debian-cd/current/amd64/iso-cd/debian-12.8.0-amd64-netinst.iso [siguiendo]
--2024-12-18 16:04:51--  https://chuangtzu.ftp.acc.umu.se/debian-cd/current/amd64/iso-cd/debian-12.8.0-amd64-netinst.iso
Resolviendo chuangtzu.ftp.acc.umu.se (chuangtzu.ftp.acc.umu.se)... 2001:6b0:19::167, 194.71.11.167
Conectando con chuangtzu.ftp.acc.umu.se (chuangtzu.ftp.acc.umu.se)[2001:6b0:19::167]:443... conectado.
Petición HTTP enviada, esperando respuesta... 200 OK
Longitud: 661651456 (631M) [application/x-iso9660-image]
Grabando a: «debian-12.8.0-amd64-netinst.iso»

debian-12.8.0-amd64 100%[===================>] 631,00M  7,26MB/s    en 90s     

2024-12-18 16:06:22 (7,01 MB/s) - «debian-12.8.0-amd64-netinst.iso» guardado [661651456/661651456]

madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
wget https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA512SUMS
--2024-12-18 16:06:56--  https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA512SUMS
Resolviendo cdimage.debian.org (cdimage.debian.org)... 2001:6b0:19::165, 2001:6b0:19::163, 194.71.11.165, ...
Conectando con cdimage.debian.org (cdimage.debian.org)[2001:6b0:19::165]:443... conectado.
Petición HTTP enviada, esperando respuesta... 200 OK
Longitud: 494
Grabando a: «SHA512SUMS»

SHA512SUMS          100%[===================>]     494  --.-KB/s    en 0s      

2024-12-18 16:06:57 (18,8 MB/s) - «SHA512SUMS» guardado [494/494]

madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
sha512sum --check --ignore-missing SHA512SUMS
debian-12.8.0-amd64-netinst.iso: La suma coincide
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 