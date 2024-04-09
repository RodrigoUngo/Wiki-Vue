[[_TOC_]]

#Archivo Dockerfile

```Docker
FROM node:9.11.1-alpine

# instalar un simple servidor http para servir nuestro contenido estático
RUN npm install -g http-server

# hacer la carpeta 'app' el directorio de trabajo actual
WORKDIR /app

# copiar 'package.json' y 'package-lock.json' (si están disponibles)
COPY package*.json ./

# instalar dependencias del proyecto
RUN npm install

# copiar los archivos y carpetas del proyecto al directorio de trabajo actual (es decir, la carpeta 'app')
COPY . .

# construir aplicación para producción minificada
RUN npm run build

EXPOSE 8080
CMD [ "http-server", "dist" ]
```

# Construir imagen Docker para aplicación Vue

En la línea de comando escriba la siguiente instrucción:
**`docker build -t vuejs-image .`**

#Ejecutar el contenedor
**`docker run -it -p 8080:8080 --rm --name vuejs-container vuejs-image`**


Referencia:
- https://es.vuejs.org/v2/cookbook/dockerize-vuejs-app