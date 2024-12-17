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

### Pasos para crear un anillo de confianza

#### 1. Subir mi clave pública a un servidor de claves
El primer paso es subir tu clave pública a un servidor de claves, en este caso lo subiremos a ```pgp.rediris.es```, de la siguiente manera:

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --keyserver pgp.rediris.es --send-key B7E822D8FB45BD8BAF2F31561B80812C7BB9EA86
gpg: enviando clave 1B80812C7BB9EA86 a hkp://pgp.rediris.es
```

#### 2. Enviar mi fingerprint a mis compañeros

Para que mis compañeros puedan descargar mi clave pública, lo que debemos enviarse es  ***fingerprint*** o tambien llamada huella digital, la cual se obtiene con el siguiente comando:

```gpg --fingerprint B7E822D8FB45BD8BAF2F31561B80812C7BB9EA86``` 

y la cual nos da la siguiente salida por pantalla:

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --fingerprint B7E822D8FB45BD8BAF2F31561B80812C7BB9EA86
pub   rsa3072 2024-12-16 [SC] [caduca: 2026-12-16]
      B7E8 22D8 FB45 BD8B AF2F  3156 1B80 812C 7BB9 EA86
uid        [  absoluta ] Andrés Morales González <asirandyglez@gmail.com>
sub   rsa3072 2024-12-16 [E] [caduca: 2026-12-16]


```

De esto que nos aparece en pantalla nos quedaremos con la línea que esta entre ***pub*** y ***uid**** y es lo que le mandaremos a nuestro compañero.

#### 3. Descargar claves públicas de mis compañeros

Procedemos a descargarnos las claves públicas de mis compañeros, usando sus ***fingerprints***, con el siguiente comando: ```gpg --keyserver pgp.rediris.es --recv-keys <fingerprint>```

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --keyserver pgp.rediris.es --recv-keys B39722468D0599C3B62F9AEA9E7BEEE532BE0469
gpg: clave 9E7BEEE532BE0469: clave pública "Alejandro Liáñez Frutos <alejandroliafru@gmail.com>" importada
gpg: Cantidad total procesada: 1
gpg:               importadas: 1
```
#### 4. Firmar las claves públicas de los compañeros

Procedemos a firmar las claves públicas descargadas para su validacia¡ón en nuestro anillo de confianza, de la siguiente manera ```gog --sign-key <fingerprint>```, por lo que en pantalla se vería de la siguiente manera:

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --sign-key B39722468D0599C3B62F9AEA9E7BEEE532BE0469

pub  rsa3072/9E7BEEE532BE0469
     creado: 2024-12-12  caduca: 2026-12-12  uso: SC  
     confianza: desconocido   validez: desconocido
sub  rsa3072/0E4BC9F6F259E1BA
     creado: 2024-12-12  caduca: 2026-12-12  uso: E   
[desconocida] (1). Alejandro Liáñez Frutos <alejandroliafru@gmail.com>


pub  rsa3072/9E7BEEE532BE0469
     creado: 2024-12-12  caduca: 2026-12-12  uso: SC  
     confianza: desconocido   validez: desconocido
 Huella clave primaria: B397 2246 8D05 99C3 B62F  9AEA 9E7B EEE5 32BE 0469

     Alejandro Liáñez Frutos <alejandroliafru@gmail.com>

Esta clave expirará el 2026-12-12.
¿Está realmente seguro de querer firmar esta clave
con su clave: "Andrés Morales González <asirandyglez@gmail.com>" (1B80812C7BB9EA86)?

¿Firmar de verdad? (s/N) s

```

#### 5. Exportar y enviar las claves firmadas

Exporta las claves públicas que firmaste y envíalas de vuelta a sus propietarios para que puedan incorporarlas a sus anillos de confianza.

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --armor --export -a B39722468D0599C3B62F9AEA9E7BEEE532BE0469 > alf.asc

```
#### 6. Imprtar claves firmadas por mis compañeros

Cuando reciba mi clave firmada por mi compañero, la importo para incluirla en mi anillo de confianza:

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --import andres.asc 
gpg: clave 1B80812C7BB9EA86: "Andrés Morales González <asirandyglez@gmail.com>" 1 firma nueva
gpg: Cantidad total procesada: 1
gpg:         nuevas firmas: 1
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: nivel: 0  validez:   1  firmada:   1  confianza: 0-, 0q, 0n, 0m, 0f, 1u
gpg: nivel: 1  validez:   1  firmada:   0  confianza: 1-, 0q, 0n, 0m, 0f, 0u
gpg: siguiente comprobación de base de datos de confianza el: 2026-12-12
```

#### 7. Verifico que mis compañeros hayan firmado mi clave

Comprueba que mi clave pública contiene las firmas de mis compañeros:

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --list-sigs B7E822D8FB45BD8BAF2F31561B80812C7BB9EA86
pub   rsa3072 2024-12-16 [SC] [caduca: 2026-12-16]
      B7E822D8FB45BD8BAF2F31561B80812C7BB9EA86
uid        [  absoluta ] Andrés Morales González <asirandyglez@gmail.com>
sig 3        1B80812C7BB9EA86 2024-12-16  Andrés Morales González <asirandyglez@gmail.com>
sig          9E7BEEE532BE0469 2024-12-17  Alejandro Liáñez Frutos <alejandroliafru@gmail.com>
sub   rsa3072 2024-12-16 [E] [caduca: 2026-12-16]
sig          1B80812C7BB9EA86 2024-12-16  Andrés Morales González <asirandyglez@gmail.com>

```
Donde nos tenemos que fijar en esta salida del comando es en las siguientes líneas:

```
sig 3        1B80812C7BB9EA86 2024-12-16  Andrés Morales González <asirandyglez@gmail.com>
sig          9E7BEEE532BE0469 2024-12-17  Alejandro Liáñez Frutos <alejandroliafru@gmail.com>
```

### Muestra las firmas que tiene tu clave pública

### Comprueba que ya puedes verificar sin “problemas” una firma recibida por una persona en la que confías.

Como he importado la clave pública de mi compañero ***Alejandro Liáñez Frutos*** en mi anillo de confianza, puedo  verificar sin ningún tipo de problemas el archivo:

```
madandy@toyota-hilux:~/Documentos/SegundoASIR/security$ 
gpg --verify fichero.txt.sig 
gpg: asumiendo que los datos firmados están en 'fichero.txt'
gpg: Firmado el lun 16 dic 2024 16:43:41 CET
gpg:                usando RSA clave B39722468D0599C3B62F9AEA9E7BEEE532BE0469
gpg: Firma correcta de "Alejandro Liáñez Frutos <alejandroliafru@gmail.com>" [total]
```
### Comprueba que puedes verificar con confianza una firma de una persona en las que no confías, pero sin embargo si confía otra persona en la que tu tienes confianza total.