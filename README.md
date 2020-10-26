# signtest

## 1. Crear una firma GPG

### Instalar GPG en tu máquina

Acude a un terminal y prueba el siguiente comando:

`gpg`

Si el resultado es que no existe ese comando, es que no lo tienes instalado.

#### Windows

Instala [GPG4Win](https://gpg4win.org/download.html).

#### Linux

En muchas distribuciones viene instalado por defecto, si no es el caso, deberás buscar en Internet cómo instalarlo para tu distribución en concreto.

En la mayoría de distribuciones este programa se llama `gnupg`.

Si tu repositorio no dispone de este programa, tendrás que acudir a la página oficial y descargarte el software, así como seguir las instrucciones de instalación.

Si te toca hacer esto último, asegúrate de estar en la página web oficial, con conexión segura (https), y verifica que el hash del software es el correcto.

Según para qué vayas a usar las firmas digitales, es importantísimo asegurarse de que el programa que descargues para generar las firmas es fiable, proviene de una fuente oficial, y no ha sido alterado o corrompido.

#### Mac

En Mac instala `brew` y después puedes instalar usando el comando `brew install gnupg`.

### Generar una nueva clave

Ejecuta el siguiente comando:

`
gpg --full-generate-key
`

Sigue los pasos.

Lo más importante puede ser que debas elegir una clave de 4096 bits, y que tenga password.

También añade tu nombre y tu correo electrónicos de GitHub. Es muy recomendable usar el email `noreply` que GitHub te tiene asignado.

Otro detalle podría ser ponerle clave de caducidad. Si no te sientes seguro de la máquina en la que vas a instalar tus firmas, es importante que al menos caduquen.

### Obtén la ID de tu llave

Cada firma tiene una ID única que lo identifica.

Para saber la ID de tu firma, ejecuta el siguiente comando:

`
gpg --list-secret-keys --keyid-format LONG
`

La ID de tu firma se encuentra en la línea "sec".

Por ejemplo:

`
sec   rsa4096/9999999999999999 2020-10-25 [SC] 
`

Tu ID entonces sería 9999999999999999 (no se incluye "rsa4096/")

## 2. Sube tu firma a GitHub

Primero necesitamos nuestra clave pública en formato texto. Para ello, ejecutamos el siguiente comando:

`
gpg --armor --export TU_ID
`

Donde TU_ID sería la ID de tu firma, que obtuviste en el paso anterior.

Se imprimirá en pantalla tu clave pública en formato texto. Cópialo, desde "----BEGIN PGP PUBLIC KEY BLOCK----" hasta "----END PGP PUBLIC KEY BLOCK----".

No olvides que ambas ----cabeceras---- deben incluirse.

Ahora ve a la web oficial de GitHub y haz login. Dirígete a:

Configuración > Claves SSH y GPG

En la sección Claves GPG pulsa el botón "Nueva clave GPG".

Pega lo que has copiado anteriormente, y pulsa guardar.

Ahora GitHub tiene la información que necesita para verificar tu firma.

## 3. Configura tu cliente GIT para usar la firma

Lo primero de todo, configura git para que conozca qué firma debe utilizar, ya que con gpg puedes tener varias firmas guardadas.

`
git config --global user.signingkey LA_ID_DE_TU_FIRMA
`

Según tu caso, podrías querer configurar algunas cuantas cosas más:

`git config --global user.gpgsign true`

`git config --global gpg.program gpg`

Con el primer comando le dices a GitHub que tus commits y tags siempre deben estar firmados.

Con el segundo comando le decimos a github que use el comando `gpg` para firmar. Esto puede variar según el sistema operativo.

## 4. Firma tus commits y tags

A partir de ahora, siempre que quieras realizar un commit firmado, añade -S al comando commit.

`git commit -S -m "commit message"`

## 5. ¿Por qué deberías firmar tus commits?

Cualquiera puede suplantar una identidad en GitHub si tan sólo configura su cliente con tu nombre y tu correo.

GitHub ofrece esta posibilidad de firmar commits y tags para permitir comprobar si un commit ha sido realmente realizado por la persona que dice ser.

## 6. Problemas que pueden surgir al configurar la firma

La primera vez que traté de firmar un commit obtuve el siguiente mensaje de error:

`
error: gpg failed to sign the data
fatal: failed to write commit object
`

Esto se resuelve declarando la siguiente variable de entorno:

`
export GPG_TTY=$(tty)
`

Si quieres que esta variable esté siempre disponible en tu terminal, añádela a tu `.bashrc` o similar.

## 7. Best practices

### Crea tus claves con contraseña

A pesar de que es posible crear una firma sin contraseña, es recomendable ponerla, especialmente si vas a trabajar en un ordenador ajeno.

Si compartes el ordenador en el trabajo, puede ser que alguien por accidente o malinterncionadamente, haga un commit usando tu configuración de git.

Si ya tienes el equipo preparado para firmar los commits, si la firma requiere contraseña, este paso será obligatorio siempre, y siempre se te pedirá antes de hacer el commit.
