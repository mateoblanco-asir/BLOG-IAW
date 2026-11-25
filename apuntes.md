# Apuntes de Git, GitHub y Docker

Resumen de las clases de control de versiones, Markdown, Docker y la práctica del blog con Jekyll. Al final hay una chuleta con todos los comandos agrupados por tema.

## Índice

1. [Git y GitHub](#1-git-y-github)
2. [Ramas, merge y stash](#2-ramas-merge-y-stash)
3. [Trabajar con GitHub](#3-trabajar-con-github)
4. [Markdown](#4-markdown)
5. [Docker](#5-docker)
6. [Práctica: blog con Jekyll y Docker Compose](#6-práctica-blog-con-jekyll-y-docker-compose)
7. [CMS y SSG](#7-cms-y-ssg)
8. [Chuleta de comandos](#8-chuleta-de-comandos)

---

## 1. Git y GitHub

### 1.1 Qué es cada uno

**Git** es un sistema de control de versiones distribuido y de código abierto, creado por Linus Torvalds en 2005. Sirve para llevar el historial de cambios de los archivos de un proyecto: guardar cambios a lo largo del tiempo (commits), volver a versiones anteriores y trabajar en paralelo con ramas. Es un programa local que se maneja por comandos y no necesita internet.

**GitHub** es una plataforma en la nube que aloja repositorios Git. Salió en 2008 y desde 2018 es de Microsoft. Permite compartir repositorios, colaborar en equipo (pull requests) y controlar los cambios de los proyectos desde cualquier sitio.

| Git | GitHub |
| --- | --- |
| Programa local (software) que se usa por línea de comandos | Servicio en la nube |
| Controla versiones | Permite compartir repositorios |
| No necesita internet, funciona offline | Necesita internet |
| Se usa con comandos | Interfaz web y también comandos |
| Open source | Propiedad de Microsoft, plataforma propietaria |

Git es el control local y GitHub es el trabajo en equipo en la nube. Juntos son la base del desarrollo colaborativo.

Alternativas a GitHub que se usan en empresas:

- **GitLab**: en la nube o instalable en servidores propios, incluye integración continua (CI/CD).
- **Bitbucket**: de Atlassian, se integra con Jira y Trello.
- **Azure DevOps Repos**: repositorios Git dentro de la suite de Microsoft, pensado para proyectos grandes.

### 1.2 Qué es un commit

Un commit es una instantánea del proyecto en un momento concreto. Cada uno lleva:

- Un identificador único, el hash SHA-1 (40 cifras hexadecimales).
- Un mensaje que explica qué se cambió.
- El autor y la fecha.

Como Git es distribuido, cada colaborador tiene una copia completa del proyecto con todo su historial.

Buenas prácticas: commits pequeños con mensajes claros, una rama por funcionalidad y pull requests con revisión.

### 1.3 Zonas de trabajo y estados de los archivos

Los archivos pasan por tres zonas en local antes de llegar al remoto:

| Zona | Qué representa | Dónde está |
| --- | --- | --- |
| Working directory | Lo que estás haciendo ahora (la carpeta del proyecto) | Local |
| Staging area | Lo que has decidido guardar en el próximo commit | Local |
| Repositorio local | Historial de cambios confirmados | Local |
| Repositorio remoto | Copia en GitHub | Nube |

Trabajamos en el working directory, elegimos los cambios con `git add` (staging) y los guardamos con `git commit` (repositorio local). Para subirlos a la nube usamos `git push`.

Estados de un archivo:

- **Untracked**: Git todavía no lo sigue. Pasa a tracked la primera vez que hacemos `git add`.
- **Tracked**: Git ya lo sigue, y puede estar en uno de estos tres estados:
  - **Unmodified**: no ha cambiado desde el último commit.
  - **Modified**: se ha editado pero todavía no está preparado para el commit.
  - **Staged**: los cambios están añadidos con `git add` y listos para el commit.

Después de un commit los archivos vuelven a unmodified.

### 1.4 Configuración inicial

Antes del primer commit hay que decirle a Git quién somos. Si no, el commit da error.

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
```

### 1.5 Primer repositorio local

1. Abrimos la terminal (Git Bash) dentro de la carpeta del proyecto.
2. Inicializamos el repositorio:

   ```bash
   git init
   ```

3. Comprobamos que se creó la carpeta oculta `.git`:

   ```bash
   ls -la
   ```

4. Creamos el archivo `index.md`.
5. Lo añadimos al área de preparación (staging):

   ```bash
   git add index.md
   ```

6. Miramos el estado:

   ```bash
   git status
   ```

7. Si nos arrepentimos, lo sacamos del staging sin borrarlo del ordenador:

   ```bash
   git rm --cached index.md
   ```

8. Hacemos el primer commit:

   ```bash
   git commit -m "added index.md"
   ```

9. Vemos el historial:

   ```bash
   git log
   ```

Para añadir varios archivos a la vez:

```bash
git add .                          # todos los cambios
git add fichero1.md fichero2.md    # solo los que indiquemos
```

Para sacar todo del staging de forma recursiva:

```bash
git rm -r --cached .
```

Hay otra forma de deshacer el `git add` en archivos que Git ya conocía (los que solo hemos modificado):

```bash
git restore --staged First.txt
```

La diferencia: `git restore --staged` es para archivos ya rastreados, que siguen tracked después. `git rm --cached` es para archivos nuevos que acabamos de añadir por primera vez, y los deja como estaban antes del primer `git add`.

La salida de `git log` tiene este aspecto:

```
commit 9c1d4e7a2b0f5a83d6c19e47b2a05f8d3e6c7b41 (HEAD -> master)
Author: Tu Nombre <tu@email.com>
Date:   Wed Sep 30 16:49:30 2026 +0200

    added index.md
```

El hash largo es el identificador SHA del commit y `HEAD -> master` indica la rama donde estamos.

### 1.6 Flujo local de un vistazo

```
archivo nuevo (untracked) --git add--> staged --git commit--> unmodified
unmodified --editar--> modified --git add--> staged
```

---

## 2. Ramas, merge y stash

### 2.1 Ramas (branches)

Una rama es una línea de desarrollo independiente. Es como una copia del proyecto donde podemos trabajar sin tocar el código principal (`main` o `master`). Cada rama tiene su propio historial de commits y podemos movernos entre ellas libremente. Sirven para desarrollar una funcionalidad nueva, probar algo o corregir un error, y así `main` se mantiene estable mientras se trabaja en paralelo. Cuando lo nuevo está listo y probado, se fusiona (merge) con la rama principal.

```bash
git branch                    # lista las ramas locales (la actual lleva un *)
git branch -a                 # lista locales y remotas
git checkout -b nueva-rama    # crea una rama y se cambia a ella
git switch -c nueva-rama      # lo mismo, con el comando más nuevo
git checkout otra-rama        # cambiar de rama
git switch otra-rama          # lo mismo
git branch -m nuevo-nombre    # renombrar la rama actual
git branch -M main            # renombrar la rama actual a main (forzando)
git branch -d rama-fusionada  # borrar una rama ya fusionada
```

Ejemplo: vamos a desarrollar el login de usuarios y no queremos tocar `main`.

```bash
git checkout -b feature/login
# trabajamos, hacemos commits, probamos...
git checkout main
git merge feature/login
```

### 2.2 Merge

`git merge` une dos líneas de desarrollo. Nos ponemos en la rama que va a recibir los cambios (normalmente `main`) y ejecutamos:

```bash
git switch main
git merge rama-uno
```

Tras la fusión, `main` avanza e incluye los cambios, y la rama de origen queda intacta: podemos seguir trabajando en ella o borrarla.

### 2.3 Conflictos de merge

Git intenta combinar los archivos solo. Si un archivo se modificó en una rama y en la otra no, no hay problema. Pero si el mismo fragmento se cambió de forma distinta en las dos ramas (por ejemplo, la línea 2 del `README.md`), Git no sabe cuál conservar y salta un conflicto.

Cuando pasa, la fusión se para y:

1. Git marca los archivos en conflicto.
2. Tenemos que elegir o combinar a mano los cambios correctos. En VS Code aparecen marcas del tipo `<<<<<<< HEAD`, `=======` y `>>>>>>>`, con opciones para aceptar el cambio actual, el entrante o ambos.
3. Una vez resuelto, hacemos `git add` del archivo y un commit para terminar la fusión.

### 2.4 Stash

`git stash` guarda temporalmente los cambios sin confirmar en un almacén oculto, para poder cambiar de rama sin perderlos ni hacer un commit de código a medias. Sin stash, Git nos impediría cambiar de rama o nos obligaría a forzarlo con riesgo de perder trabajo. Es útil, por ejemplo, cuando hay que atender un bug urgente en otra rama.

```bash
git stash                          # guarda los cambios (también: git stash push)
git stash save "mensaje"           # guarda con una descripción
git stash list                     # lista los stashes: stash@{0}, stash@{1}...
git stash apply                    # aplica el stash y lo mantiene en la lista
git stash pop                      # aplica el stash y lo elimina (apply + drop)
git stash drop stash@{0}           # elimina un stash concreto
git stash clear                    # elimina todos
git stash -u                       # incluye archivos untracked
git stash -a                       # incluye también los ignorados por .gitignore
```

Por defecto solo se guardan los archivos que Git ya rastrea. Los stashes funcionan como una pila (stack), por eso se identifican con índices, y conviene limpiar los antiguos para no liarse.

---

## 3. Trabajar con GitHub

### 3.1 Crear un repositorio en GitHub y conectarlo con el local

Al crear un repositorio en GitHub aparecen dos opciones:

- Si todavía no hemos hecho `git init` ni ningún commit: **create a new repository on the command line**.
- Si ya tenemos un repositorio local con commits: **push an existing repository from the command line**.

Práctica 1 (repositorio privado y vacío, sin README, sin `.gitignore` y sin licencia):

```bash
echo "# gitprueba-iaw" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/<tu-usuario>/gitprueba-iaw.git
git push -u origin main
```

La opción `-u` deja enlazada la rama local con la remota, así que después basta con `git push` y `git pull` a secas.

### 3.2 Clonar un repositorio

Práctica 2: creamos en GitHub el repositorio `gitpr-iaw`, esta vez público y con README, y lo clonamos en una carpeta nueva:

```bash
git clone https://github.com/<tu-usuario>/gitpr-iaw.git
```

Al clonar, el remoto `origin` ya queda configurado, así que no hace falta `git init` ni `git remote add origin`. Lo comprobamos con:

```bash
git status
git remote -v
```

```
origin  https://github.com/<tu-usuario>/gitpr-iaw.git (fetch)
origin  https://github.com/<tu-usuario>/gitpr-iaw.git (push)
```

- **fetch**: la URL de la que Git descargará las actualizaciones cuando hagamos `git pull`.
- **push**: la URL a la que enviará nuestros cambios cuando hagamos `git push`.

### 3.3 Push, pull, pull request y merge: en qué se diferencian

| Qué | Para qué sirve |
| --- | --- |
| `git push` | Enviamos los commits de una rama local al repositorio remoto |
| `git pull` | Traemos los cambios del remoto y los integramos en la rama local |
| Pull request (PR) | En GitHub, una propuesta para revisar e incorporar los cambios de una rama en otra |
| `git merge` | Integramos desde Git el historial de otra rama en la actual |
| Merge pull request | Integramos en GitHub los cambios propuestos en la PR |

Ojo con dos cosas: hacer `git push` no crea una PR automáticamente, y abrir una PR no mete los cambios en `main`. La integración solo ocurre cuando se pulsa el botón de merge.

### 3.4 Flujo de trabajo en equipo con pull requests

Una pull request es una solicitud para incorporar los cambios de una rama en otra. Permite explicar qué se ha cambiado, revisar las diferencias y comentarlas con los compañeros antes de fusionar.

1. Creamos una rama de trabajo a partir de `main`:

   ```bash
   git checkout -b mejora-readme      # o: git switch -c mejora-readme
   git branch                         # comprobamos en qué rama estamos
   ```

2. Modificamos el archivo (por ejemplo, añadimos algo al final del `README.md`) y hacemos el commit:

   ```bash
   git add README.md
   git commit -m "mejora del readme"
   ```

3. Publicamos la rama en GitHub:

   ```bash
   git push -u origin mejora-readme
   ```

4. En GitHub abrimos la pull request, eligiendo la rama de origen (`mejora-readme`) y la de destino (`main`).
5. Revisamos los cambios, dejamos comentarios y respondemos a las peticiones de mejora.
6. Cuando está todo bien, pulsamos **Merge pull request**.
7. En local volvemos a `main` y traemos lo nuevo con `git pull`.

### 3.5 Práctica de coevaluación en parejas

Dos compañeros, A y B, se corrigen mutuamente a través de GitHub:

1. A crea una carpeta de trabajo (por ejemplo, `correccion-b`) y clona en ella el repositorio de B con `git clone <url-del-compañero>`.
2. A crea una rama de trabajo.
3. A crea el archivo de coevaluación, lo rellena, hace `git add` y `git commit`, y publica la rama con `git push`.
4. B, desde VS Code o la terminal, hace `git pull` y le aparece el archivo de coevaluación de su compañero.

---

## 4. Markdown

Markdown es un lenguaje de marcado ligero, creado por John Gruber y Aaron Swartz, que facilita escribir y leer documentos en texto plano con unas pocas marcas sencillas. No hay un estándar único, así que existen varios sabores (flavors). En clase usamos el de GitHub, el que se usa en los `README.md` y en la documentación técnica de los proyectos.

El proceso tiene cuatro pasos: se crea el archivo `.md` con un editor, se abre en una aplicación Markdown, esta lo convierte a HTML y ese HTML se ve en el navegador o se convierte a otro formato como PDF. En VS Code, la vista previa se abre con `Ctrl + Shift + V`.

| Elemento | Sintaxis |
| --- | --- |
| Encabezados | `# h1`, `## h2`, `### h3`... hasta `###### h6` |
| Negrita | `**texto**` o `__texto__` |
| Cursiva | `*texto*` o `_texto_` |
| Comando o código en línea | entre acentos graves: `` `ls -la` `` |
| Bloque de código | tres acentos graves al abrir y al cerrar; después de los primeros se puede indicar el lenguaje (bash, python, yaml, json, html, javascript...) para resaltar la sintaxis |
| Enlace | `[texto del enlace](https://www.google.com)` |
| Imagen | `![texto alternativo](https://url-de-la-imagen.png)` |
| Lista sin orden | `* Item 1` (se anidan con sangría) |
| Lista ordenada | `1. Item 1` (también se anidan con sangría) |
| Tabla | ver el ejemplo de abajo |

Ejemplo de tabla:

```markdown
| Encabezado 1 | Encabezado 2 | Encabezado 3 |
| --- | --- | --- |
| Fila 1.1 | Fila 1.2 | Fila 1.3 |
| Fila 2.1 | Fila 2.2 | Fila 2.3 |
```

---

## 5. Docker

### 5.1 Qué es

Docker es una plataforma para crear, desplegar y ejecutar aplicaciones dentro de contenedores. Un contenedor se parece a una mini máquina virtual pero es mucho más ligero: lleva solo lo necesario para ejecutar la aplicación (código, librerías, dependencias) y comparte el kernel del equipo en lugar de montar un sistema operativo completo. Así podemos tener el servidor web, la base de datos y la aplicación en contenedores separados que funcionan igual en cualquier ordenador.

### 5.2 Piezas del ecosistema

| Pieza | Qué hace |
| --- | --- |
| Docker Client (CLI) | La interfaz de comandos (`docker run`, `docker ps`, `docker build`). Hace de intermediario entre nosotros y el servidor |
| Docker Server / Daemon | El servicio que corre en segundo plano y hace el trabajo real: descarga imágenes, crea y ejecuta contenedores, gestiona redes, volúmenes y recursos. La CLI solo le manda peticiones |
| Docker Hub | El registro (público o privado) donde se guardan las imágenes. Con `docker run redis`, el servidor busca la imagen en local y, si no está, la descarga de Docker Hub. Para subir las nuestras se usa `docker push` |
| Docker Images | Plantillas inmutables con la aplicación, las dependencias y la configuración. De ellas se crean los contenedores |
| Docker Compose | Herramienta para definir y ejecutar varios contenedores a la vez con un único archivo |
| Docker Machine | Está en desuso. Creaba y aprovisionaba hosts con Docker. Hoy lo hace Docker Desktop en local, y en la nube se usan Terraform o Ansible |

### 5.3 Imagen y contenedor

- La **imagen** es la plantilla inmutable.
- El **contenedor** es una instancia de esa imagen en ejecución, aislada y portable.
- De una misma imagen se pueden crear tantos contenedores como queramos, cada uno con su propio proceso aislado.

El recorrido completo es: Dockerfile → (`build`) → imagen → (`run`) → contenedor.

### 5.4 Qué pasa cuando ejecutamos un comando

Tomamos como ejemplo `docker run`:

1. La CLI envía la petición al Docker Server.
2. El servidor busca la imagen en local y, si no la encuentra, la descarga de un registro como Docker Hub.
3. Con esa imagen crea el contenedor, con sus propios procesos, red, sistema de archivos, variables de entorno y límites de CPU y RAM.
4. El contenedor arranca el proceso principal definido en la imagen y expone los puertos y volúmenes configurados.
5. Cuando el proceso termina, el contenedor se detiene. Se puede reiniciar, volver a crear o eliminar sin afectar a la imagen original.

### 5.5 Por qué usar Docker

Instalar software a mano suele ser una cadena de instalador, error, buscar en Google, parche, otro error y vuelta a empezar, con conflictos de dependencias, versiones mezcladas en el sistema y diferencias entre el equipo de desarrollo y los servidores. Con Docker, todo el entorno (dependencias, versiones y configuración) viaja empaquetado con la aplicación en una imagen, y lo que funciona en nuestra máquina funciona en cualquier otra.

Motivos principales:

1. **Aísla los entornos de desarrollo**: cada aplicación va en su contenedor con sus versiones. Se acaba el "en mi ordenador funciona", y se pueden tener PHP 7.4 y PHP 8.2 a la vez en la misma máquina.
2. **Facilita el despliegue**: es repetible, rápido y automatizable. Lo que funciona en local funciona igual en Azure, AWS o un VPS.
3. **Integra servicios** (multicontenedor): servidor web, base de datos y backend se levantan juntos con un archivo y un comando.
4. **Arquitectura moderna**: es el estándar de la industria, tanto para crear entornos de prueba como para desplegar en la nube.
5. **Simula la infraestructura real**: mapeo de puertos y redes, volúmenes para guardar datos, variables de entorno y secretos, y captura de logs.

Beneficios clave: aislamiento, consistencia entre desarrollo, pruebas y producción, rapidez (arranca en segundos y el rollback es fácil gracias al versionado de imágenes), portabilidad (Windows, macOS y Linux) y escalabilidad con orquestadores como Docker Compose o Kubernetes.

### 5.6 Docker Compose y YAML

Docker Compose permite describir y gestionar un entorno de varios servicios con un archivo YAML (`docker-compose.yml`): imágenes, volúmenes, puertos, redes, dependencias y comando de arranque. Con un solo `docker compose up` levanta y conecta todo. Es ideal para aplicaciones con varios servicios, como web + base de datos + caché.

Lo básico de YAML:

- `#` para comentarios.
- `-` para marcar los elementos de una lista.
- `|` para conservar los saltos de línea de un script.
- La estructura se define con la indentación (con espacios, no con tabuladores).

### 5.7 Comandos de Docker

```bash
docker run <imagen>             # crea y arranca un contenedor (descarga la imagen si hace falta)
docker ps                       # lista los contenedores
docker build                    # construye una imagen a partir de un Dockerfile
docker push                     # sube una imagen propia a Docker Hub
docker compose config --quiet   # valida el docker-compose.yml (si no muestra nada, está bien)
docker compose up -d            # crea y arranca los servicios en segundo plano
```

---

## 6. Práctica: blog con Jekyll y Docker Compose

El objetivo es levantar un blog con Jekyll dentro de un contenedor y publicarlo en GitHub Pages.

### Paso 1. Carpeta de trabajo

```bash
mkdir blog-iaw
cd blog-iaw
```

Abrimos la carpeta en VS Code y creamos el archivo `docker-compose.yml`.

### Paso 2. Elegir la imagen

La imagen es la plantilla con la que Docker crea el contenedor. Aquí necesitamos Ruby, Bundler y Jekyll:

- **Ruby** es el lenguaje de programación.
- **Bundler** lee las dependencias del archivo `Gemfile` y las instala.

Todas las imágenes oficiales están en Docker Hub. Las oficiales no llevan nombre de usuario delante y aparecen con la marca **Official Image**. Usamos `ruby:3.3-bookworm` porque trae Ruby, Bundler y las herramientas para instalar lo que Jekyll necesita.

### Paso 3. El archivo docker-compose.yml

```yaml
services:
  jekyll:
    image: ruby:3.3-bookworm
    # working_dir hace que el comando de arranque empiece en /proyecto dentro del contenedor
    working_dir: /proyecto
    # ejecuta el proceso con este usuario; si las variables están vacías usa 1000:1000
    user: "${JEKYLL_UID:-1000}:${JEKYLL_GID:-1000}"
    # variables de entorno
    environment:
      BUNDLE_USER_HOME: /tmp/jekyll-bundler
    volumes:
      # carpeta del proyecto en nuestro equipo -> /proyecto en el contenedor
      - .:/proyecto
      # Docker guarda aquí las dependencias de Ruby
      - gemas:/usr/local/bundle
    ports:
      # IP local : puerto de nuestro equipo : puerto del contenedor
      - "127.0.0.1:4000:4000"
      # segundo puerto para que el navegador reciba los avisos de recarga
      - "127.0.0.1:35729:35729"
    command:
      - sh
      - -ec
      - |
        # solo en el primer arranque de un proyecto vacío
        if [ ! -f docs/Gemfile ]; then
          gem install jekyll --version 3.10.0 --no-document
          jekyll _3.10.0_ new docs --skip-bundle
        fi
        # Bundler instala las dependencias que falten y luego arranca Jekyll
        cd docs
        bundle check || bundle install
        exec bundle exec jekyll serve --host 0.0.0.0 --port 4000 \
          --watch --force_polling --livereload --livereload-port 35729

volumes:
  gemas:
```

### Paso 4. Arrancar el entorno

Primero comprobamos que el YAML está bien escrito. Si el comando termina sin mostrar nada, todo correcto:

```bash
docker compose config --quiet
```

Después creamos y arrancamos el servicio. Hay que tener **Docker Desktop abierto** cuando lo ejecutemos:

```bash
docker compose up -d
```

Al terminar se crea la carpeta `docs` en el proyecto con estos archivos:

| Archivo o carpeta | Para qué sirve |
| --- | --- |
| `_config.yml` | Configuración del sitio: título, tema, etc. |
| `index.md` | La portada, que usa el tema para mostrar la lista de entradas |
| `_posts/` | Carpeta con las entradas del blog en formato Markdown |
| `Gemfile` | Declara las dependencias |
| `Gemfile.lock` | Registra las versiones exactas que instaló Bundler |
| `_site/` | El sitio ya generado |

Abrimos el navegador en `http://localhost:4000` y deberíamos ver la portada del tema.

### Paso 5. Crear un post

Jekyll reconoce una entrada por estar dentro de la carpeta `_posts` y por el nombre del archivo, que debe seguir el formato `AAAA-MM-DD-titulo.md`. Por ejemplo:

```
docs/_posts/2026-11-17-mi-primer-post.md
```

### Paso 6. Subirlo a GitHub

1. Creamos el repositorio `blog-iaw` en GitHub: público, sin README, sin licencia y sin `.gitignore`.
2. Hacemos el primer push desde la carpeta del proyecto:

   ```bash
   git init
   git add .
   git commit -m "first commit"
   git branch -M main
   git remote add origin https://github.com/<tu-usuario>/blog-iaw.git
   git push -u origin main
   ```

### Paso 7. Publicar con GitHub Pages

1. En el repositorio, entramos en **Settings → Pages**.
2. En **Build and deployment**, en *Source* elegimos **Deploy from a branch**.
3. Seleccionamos la rama `main` y la carpeta `/docs`, y pulsamos **Save**.

### Paso 8. Arreglar la ruta base

Ahora el sitio no vive en la raíz sino en la dirección de nuestro repositorio, así que hay que ajustar las claves del sitio en `docs/_config.yml`:

```yaml
url: "https://<tu-usuario>.github.io"
baseurl: "/blog-iaw"
```

---

## 7. CMS y SSG

**CMS** (Content Management System, sistema de gestión de contenidos): una aplicación con panel de administración donde se crea y edita el contenido sin tocar código. El contenido vive en una base de datos y el servidor construye cada página cuando alguien la visita. Ejemplos: WordPress, Joomla, Drupal. Es cómodo para quien no sabe programar y tiene muchos plugins, pero necesita un servidor con base de datos y PHP, es más pesado y hay que mantenerlo y actualizarlo para que no sea una puerta de entrada para ataques.

**SSG** (Static Site Generator, generador de sitios estáticos): una herramienta que coge archivos de contenido (normalmente Markdown), plantillas y configuración, y genera de una vez todas las páginas en HTML. Esas páginas ya hechas se suben a cualquier hosting, sin base de datos ni código en el servidor. Ejemplos: Jekyll, Hugo, Eleventy. Es muy rápido, seguro y barato (incluso gratis con GitHub Pages), y el contenido se versiona con Git. A cambio no hay panel visual, hay que regenerar el sitio con cada cambio y las funciones dinámicas (comentarios, login) necesitan servicios externos.

| | CMS | SSG |
| --- | --- | --- |
| Contenido | En base de datos | En archivos (Markdown) |
| Páginas | Se generan en cada visita | Se generan una sola vez, al construir el sitio |
| Servidor | Necesita PHP y base de datos | Vale cualquier hosting de archivos |
| Edición | Panel de administración | Editor de texto y Git |
| Seguridad y mantenimiento | Más superficie de ataque y más actualizaciones | Muy poca superficie de ataque |
| Ejemplos | WordPress, Joomla, Drupal | Jekyll, Hugo, Eleventy |

En nuestra práctica usamos Jekyll, que es un SSG escrito en Ruby: escribimos los posts en Markdown, Jekyll genera el HTML dentro del contenedor de Docker y GitHub Pages lo publica.

---

## 8. Chuleta de comandos

### Terminal

| Comando | Qué hace |
| --- | --- |
| `ls -la` | Lista los archivos, incluidos los ocultos (se ve `.git`) |
| `mkdir nombre` | Crea una carpeta |
| `cd nombre` | Entra en la carpeta |
| `echo "texto" >> archivo` | Añade una línea de texto al final de un archivo (lo crea si no existe) |

### Git: configuración y repositorio

| Comando | Qué hace |
| --- | --- |
| `git config --global user.name "Tu Nombre"` | Configura el nombre del autor |
| `git config --global user.email "tu@email.com"` | Configura el email del autor |
| `git init` | Inicializa un repositorio local |
| `git clone <url>` | Clona un repositorio remoto (ya deja configurado `origin`) |
| `git status` | Muestra el estado de los archivos |
| `git log` | Muestra el historial de commits |

### Git: staging y commits

| Comando | Qué hace |
| --- | --- |
| `git add archivo` | Añade un archivo al staging |
| `git add fichero1 fichero2` | Añade varios archivos concretos |
| `git add .` | Añade todos los cambios |
| `git commit -m "mensaje"` | Guarda el commit en el repositorio local |
| `git restore --staged archivo` | Saca del staging un archivo ya rastreado |
| `git rm --cached archivo` | Saca del staging un archivo nuevo sin borrarlo del disco |
| `git rm -r --cached .` | Saca del staging todo, de forma recursiva |

### Git: ramas y merge

| Comando | Qué hace |
| --- | --- |
| `git branch` | Lista las ramas locales |
| `git branch -a` | Lista ramas locales y remotas |
| `git checkout -b rama` / `git switch -c rama` | Crea una rama y cambia a ella |
| `git checkout rama` / `git switch rama` | Cambia de rama |
| `git branch -m nombre` | Renombra la rama actual |
| `git branch -M main` | Renombra la rama actual a `main` |
| `git branch -d rama` | Borra una rama ya fusionada |
| `git merge rama` | Fusiona esa rama en la actual |

### Git: stash

| Comando | Qué hace |
| --- | --- |
| `git stash` / `git stash push` | Guarda los cambios sin confirmar |
| `git stash save "mensaje"` | Lo mismo, con descripción |
| `git stash list` | Lista los stashes |
| `git stash apply` | Aplica el stash y lo mantiene |
| `git stash pop` | Aplica el stash y lo elimina |
| `git stash drop stash@{0}` | Elimina un stash concreto |
| `git stash clear` | Elimina todos |
| `git stash -u` | Incluye los archivos untracked |
| `git stash -a` | Incluye también los ignorados |

### Git: remoto

| Comando | Qué hace |
| --- | --- |
| `git remote add origin <url>` | Conecta el repositorio local con el remoto |
| `git remote -v` | Muestra las URLs de fetch y push |
| `git push -u origin <rama>` | Sube la rama y la enlaza con la remota |
| `git push` | Sube los commits (una vez enlazada la rama) |
| `git pull` | Trae y fusiona los cambios del remoto |

### Docker

| Comando | Qué hace |
| --- | --- |
| `docker run <imagen>` | Crea y arranca un contenedor |
| `docker ps` | Lista los contenedores |
| `docker build` | Construye una imagen desde un Dockerfile |
| `docker push` | Sube una imagen a Docker Hub |
| `docker compose config --quiet` | Valida el `docker-compose.yml` |
| `docker compose up -d` | Crea y arranca los servicios en segundo plano |

### Jekyll (dentro del contenedor)

| Comando | Qué hace |
| --- | --- |
| `gem install jekyll --version 3.10.0 --no-document` | Instala Jekyll sin la documentación |
| `jekyll _3.10.0_ new docs --skip-bundle` | Crea el sitio nuevo en la carpeta `docs` |
| `bundle check \|\| bundle install` | Instala las dependencias solo si faltan |
| `bundle exec jekyll serve ...` | Arranca el servidor de desarrollo con recarga automática |
