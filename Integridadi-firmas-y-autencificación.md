# Integridad, firmas y autenticación

Si vas a empezar esta práctica debes de tener los conceptos claros que explico en este [documento](./Practicas/Cifadro-Asimétrico.md), ya que sin este no podrás realizar esta práctica.

## Tarea 1: Firmas electrónicas

En este primer apartado vamos a trabajar con las firmas eléctronicas, por lo que vamos a apoyarnos en los siguientes enlaces:

- [Intercambio de claves](https://www.gnupg.org/gph/es/manual/x75.html)
- [Validación de otras claves en nuestro anillo de claves públicas](https://www.gnupg.org/gph/es/manual/x354.html)
- [Firmado de claves (Debian)](https://www.debian.org/events/keysigning.es.html)


### Manda un documento y la firma electrónica del mismo a un compañero. Verifica la firma que tú has recibido.

Para esta tarea voy a previsualizar las claves que tengo en mi ***keyring*** para ver desde que punto partire, pro lo que ejeuctaré el siguiente comando:

```gpg --list-keys```

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --list-keys
/home/madandy/.gnupg/pubring.kbx
--------------------------------
pub   rsa3072 2024-12-12 [SC] [caduca: 2026-12-12]
      B39722468D0599C3B62F9AEA9E7BEEE532BE0469
uid        [desconocida] Alejandro Liáñez Frutos <alejandroliafru@gmail.com>
sub   rsa3072 2024-12-12 [E] [caduca: 2026-12-12]

pub   rsa3072 2024-12-16 [SC] [caduca: 2026-12-16]
      B7E822D8FB45BD8BAF2F31561B80812C7BB9EA86
uid        [  absoluta ] Andrés Morales González <asirandyglez@gmail.com>
sub   rsa3072 2024-12-16 [E] [caduca: 2026-12-16]
```

Como podemos ver, las claves existentes en mi anillo de claves es la mía personal y la de ***Alejandro Liáñez Frutos***, así que procederemos a subir dicha clave a un servidor de claves, como por ejemplo, keys.gnupg.net (haciendo uso del fingerprint para así poder identificarla). Para ello, haremos uso del comando:

```gpg --keyserver keys.gnupg.net --send-key <ID> ```

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --keyserver keys.gnupg.net --send-key B7E822D8FB45BD8BAF2F31561B80812C7BB9EA86
gpg: enviando clave 1B80812C7BB9EA86 a hkp://pgp.surf.nl
```

La clave ya ha sido subida al servidor de claves, de manera que la persona que reciba mi fichero firmado podrá importarla para verificar mi firma.

Podríamos firmar un fichero e incluir la firma en el mismo, de manera que estaría unificado ```(opción --sign)```, o bien podríamos tener el fichero por un lado, y la firma por otro, tal y como vamos a hacer en esta ocasión ```(opción --detach-sign)```.

En este caso, vamos a firmar un fichero de nombre *espartaco.pdf* y a separar la firma en un fichero aparte, ejecutando para ello el comando:

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --detach-sign espartaco.pdf 
```

Para llevar a cabo el proceso de firmado, nos pedirá la frase de paso de nuestra clave privada, así que la introduciremos. Tras ello, listaremos el contenido del directorio actual, estableciendo un filtro por nombre:

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
ls -l | egrep 'espartaco'
-rw-r--r-- 1 madandy madandy 108265 dic 16 16:53 espartaco.pdf
-rw-r--r-- 1 madandy madandy    438 dic 16 16:54 espartaco.pdf.sig
```
Despues vamos a comprobar que esta firmado por nosotros antes de mandarseloa  nuestro compañero, con el siguiente comando:

```gpg --verify <fichero.sig o .asc> <fichero>```

Por lo que en este caso en concreto se veriía de la siguiente manera:

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --verify espartaco.pdf.sig espartaco.pdf 
gpg: Firmado el lun 16 dic 2024 17:55:59 CET
gpg:                usando RSA clave B7E822D8FB45BD8BAF2F31561B80812C7BB9EA86
gpg: Firma correcta de "Andrés Morales González <asirandyglez@gmail.com>" [absoluta]
```

Como se puede apreciar, el fichero original (espartaco.pdf) y la firma (espartaco.pdf.sig) se encuentran en ficheros separados, de manera que podríamos enviarlos a un compañero para que así verificase la integridad del fichero, haciendo uso de nuestra clave pública.

Nuestro compañero (Alejandro Liáñez Frutos) ha realizado de forma paralela el mismo procedimiento que nosotros, por lo tanto, antes de tratar de verificar la firma de dicho fichero, tendré que importar la clave pública de la persona que lo ha firmado. Para ello, ejecutaré el comando:

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --verify fichero.txt.sig fichero.txt
gpg: Firmado el lun 16 dic 2024 16:43:41 CET
gpg:                usando RSA clave B39722468D0599C3B62F9AEA9E7BEEE532BE0469
gpg: Firma correcta de "Alejandro Liáñez Frutos <alejandroliafru@gmail.com>" [desconocido]
gpg: ATENCIÓN: ¡Esta clave no está certificada por una firma de confianza!
gpg:          No hay indicios de que la firma pertenezca al propietario.
Huellas dactilares de la clave primaria: B397 2246 8D05 99C3 B62F  9AEA 9E7B EEE5 32BE 0469
```

Como podemos ver la firma es correcta, tal y como nos ha informado la salida del comando. Pero si nos fijamos con más detenimiento, este nos devuelve un mensaje en el cual nos informa que la clave no está certificada por una firma de confianza, por lo que no hay indicios de que la firma sea de quien dice ser. Esto se debe a que no tenemos validez en la clave pública que hemos importado, en este caso en la de ***Alejandro Liáñez Frutos***, o para que nos entendamos, aún no hemos firmado ni tampoco tenemos personas intermediarias con las que validar la clave de forma indirecta.

Ahora para seguir con la práctica, lo que haré será eliminar la clave pública de ***Alejandro Liáñez Frutos*** de mi anillo de claves, para asi poder empezar desde un punto completamente limpio e impoluto, solamente con mi clave en mi anillo, por lo que he hecho uso de la siguiente opción de comando ```--delete-keys```:

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --delete-keys B39722468D0599C3B62F9AEA9E7BEEE532BE0469
gpg (GnuPG) 2.2.40; Copyright (C) 2022 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.


pub  rsa3072/9E7BEEE532BE0469 2024-12-12 Alejandro Liáñez Frutos <alejandroliafru@gmail.com>

¿Eliminar esta clave del anillo? (s/N) s

```
Con esto la clave pública de ***Alejandro Liáñez Frutos*** ha sido eliminada de mi keyring, y haremos la comprobación de que se ha ejecutado con exito:

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --list-keys
/home/madandy/.gnupg/pubring.kbx
--------------------------------
pub   rsa3072 2024-12-16 [SC] [caduca: 2026-12-16]
      B7E822D8FB45BD8BAF2F31561B80812C7BB9EA86
uid        [  absoluta ] Andrés Morales González <asirandyglez@gmail.com>
sub   rsa3072 2024-12-16 [E] [caduca: 2026-12-16]
```

### Crea un anillo de confianza entre los miembros de la clase.

En el uso habitual de los pares de claves GPG, surge un problema crucial: la confianza. Es indispensable contar con un método que nos permita confirmar que las claves públicas que utilizamos realmente pertenecen a la persona que afirman representar. Esto nos asegura que, al cifrar un archivo con una clave pública, el destinatario sea quien corresponde y no un impostor. Asimismo, cuando verificamos la firma de un archivo, debemos tener la certeza de que la persona que lo firmó es realmente quien dice ser.

Una solución posible es establecer un anillo de confianza, que consiste en un grupo reducido de personas que se conocen entre sí. Para demostrar que confiamos en la clave pública de alguien, firmamos la huella digital (o fingerprint) de dicha clave utilizando nuestra clave privada. Luego, enviamos la clave firmada al propietario, quien puede importarla y redistribuirla con nuestra firma añadida. Esto aumenta la autenticidad y la confianza en esa clave.

Gracias a este mecanismo, si confiamos en alguien que nos envió su clave pública y esa persona a su vez valida la clave de una tercera persona (firmándola), podríamos aceptar esta tercera clave como válida. Esto depende de nuestra configuración y se basa en la relación de confianza compartida. En este sistema, la responsabilidad de validar claves públicas recae en las personas en las que confiamos. Puede parecer un poco complejo, así que usaremos un ejemplo para aclararlo.

