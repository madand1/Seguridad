madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
wget https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA512SUMS.sign
--2024-12-18 16:12:31--  https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA512SUMS.sign
Resolviendo cdimage.debian.org (cdimage.debian.org)... 2001:6b0:19::163, 2001:6b0:19::165, 194.71.11.165, ...
Conectando con cdimage.debian.org (cdimage.debian.org)[2001:6b0:19::163]:443... conectado.
Petición HTTP enviada, esperando respuesta... 200 OK
Longitud: 833
Grabando a: «SHA512SUMS.sign»

SHA512SUMS.sign     100%[===================>]     833  --.-KB/s    en 0s      

2024-12-18 16:12:32 (16,6 MB/s) - «SHA512SUMS.sign» guardado [833/833]

madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --verify SHA512SUMS.sign SHA512SUMS
gpg: Firmado el sáb 09 nov 2024 17:35:02 CET
gpg:                usando RSA clave DF9B9C49EAA9298432589D76DA87E80D6294BE9B
gpg: Imposible comprobar la firma: No public key
