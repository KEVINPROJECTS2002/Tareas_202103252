# KEVIN ESTUARDO DEL CID QUEZADA - 202103252


## TAREA 2 - LABORATORIO ANALISIS Y DISEÑO 1

# <div align="center"><img src="https://user-images.githubusercontent.com/74038190/235223599-0eadbd7c-c916-4f24-af9d-9242730e6172.gif" width="30px" /> &nbsp; Despliegue de Frontend en Docker de manera local &nbsp; <img src="https://user-images.githubusercontent.com/74038190/235223599-0eadbd7c-c916-4f24-af9d-9242730e6172.gif" width="30px" />

Se asume tener instalado un Framework previamente, para fines de este ejemplo se utilizará angular en su version 15, en donde se creará un componente que contendra un botón que al presionarlo se muestre información sobre mi persona en el curso.

### PASO 1 - CREAR UN DOCKERFILE:

Crearemos un archivo Dockerfile en la raiz del proyecto  (Angular en mi caso) y este archivo contendra el siguiente script:

```dockerfile
# Usa una imagen base de Node.js para construir la aplicación
FROM node:18 AS build

# Establece el directorio de trabajo
WORKDIR /app

# Copia los archivos del proyecto
COPY package.json package-lock.json ./
RUN npm install

# Copia el resto de los archivos y construye la aplicación
COPY . .
RUN npm run build --prod

# Usa una imagen ligera de Nginx para servir la aplicación
FROM nginx:alpine

# Copia los archivos construidos a la carpeta de Nginx
COPY --from=build /app/dist/tarea2 /usr/share/nginx/html

# Expone el puerto 80
EXPOSE 80

# Arranca Nginx
CMD ["nginx", "-g", "daemon off;"]

```

Cada linea esta comentada con lo que realiza, como se puede observar se esta utilizando una imagen de node en su version 18 y se expone el proyecto en el puerto 80

Nota: Una de las pecuilaridades de usar contenedores de docker, es que al usar una imagen podemos usar cualquier versión, ya que si bien esta alojado localmente al ser un proyecto contenido no necesita coincidir con la versión que tenemos instalada en nuestra computadora, Sin embargo, se recomienda usar una versión que coincida o que no haya sufrido muchos cambios, para que la aplicación funcione sin errores de compatibilidad.

Tambien debemos crear un docker-compose.yml en la raiz del proyecto el cual contenda la siguiente estructura:

```docker-compose
version: '3'
services:
  web:
    build: .
    ports:
      - "80:80"
```

### PASO 2 - CONSTRUIR LA IMAGEN:

Se utiliza el siguiente comando para poder construir la imagen, esto puede tardar unos minutos:

```cmd
docker build -t tarea2-angular .
```

En este caso se usa esa estructura para hacer mención a la tarea.

El comando deberia mostrar algo como esto:

<img src="Imagenes/intro.png" width="600" height="400">

### PASO 3 - LEVANTAR EL CONTENEDOR:

Se usa el siguiente comando, para levantar nuestro contenedor de docker con su respectiva imagen creada.

```cmd
docker run -d -p 80:80 tarea2-angular
```

El comando deberia dar la siguiente salida:

```cmd
\Users\Kevin\Downloads\Tareas_202103252\Tarea2>docker run -d -p 80:80 tarea2-angular
3ccec2749691a413b7aed3691db176d43fc0369e4dd2c4abb0d34c3a41a06dd0
```

que hace referencia al id del contenedor.

### PASO 4 - VISUALIZAR EN EL PUERTO LOCAL 80:

Vamos a visualizar que en el puerto 80 local, y debe aparecer nuestro proyecto de angular listo para usarse.

<img src="Imagenes/intro.png" width="600" height="400">

