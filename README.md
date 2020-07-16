# Sputnikfy

Despliegue en Kubernetes del Trabajo Práctico de la materia Programación Distribuida II de la Universidad Nacional de Avellaneda.


## Requisitos

Para usar la aplicación es necesario contar con Docker y Kubernetes instalado

## Instalación

El primer paso es iniciar minikube

```
minikube start
```

Luego, si no tenemos las imágenes de los servicios a utilizar debemos construirlas

```
eval $(minikube docker-env)
docker build -t SERVICIO:latest ./SERVICIO
```

Debemos hacer esto para los siguientes servicios:

* sputnikfy
* music-service
* users-service
* statistics-service
* statistics-listener
* music-db
* users-db
* statistics-db

Una vez hecho esto podremos levantar los Deployments y Servicios mediante el comando:

```
kubectl create -f ./kube
```

## Modo de uso

### XML de Actividad

Sputnikfy recibe XML de actividad en el puerto 8080 del URL raíz del servicio sputnikfy.

```
http://sputnikfy:8080/
```

Para realizar esta validación es necesario enviar en un POST un archivo XML con el nombre de campo "file". La aplicación devolvera un JSON con los siguientes campos:

* validation: Booleano con el resultado de la validacion
* error: String con el error de la validacion. Si se tratara de un archivo valido, el mismo será null.

El código HTTP será 200 si se trata de un archivo valido y un 406 de uno no valido.

Para probar la validación de Sputnikfy podremos enviar un archivo de la siguiente manera:

```
curl -X POST -F "file=@test/actividad_valid.xml" http://sputnikfy:8080/
```

Deberíamos obtener la siguiente respuesta:

```
{
  "validation":true,
  "error":null
}
```

### Queries

Sputnikfy recibe consultas en el puerto 8080 del servicio queries-service.

```
http://queries-service:8080/
```

#### Entidades más escuchadas

Para ver la entidad más escuchada en un rango de fechas debemos proveer las fechas en el formato YYYY-MM-DD. Debemos ingresar:

* fechaStart
* fechaEnd

#####  Cancion

```
/escuchas/cancion?fechaStart=2020-05-01&fechaEnd=2020-06-01
```

```
{
	"id_cancion":"6b6f59fd-6646-426d-8b30-9e72eba85a9d",
	"nombre":"Getting Better"
}
```

##### Album

```
/escuchas/album?fechaStart=2020-05-01&fechaEnd=2020-06-01
```

```
{
	"id_ambito":"c0524bb7-179a-47e5-a3b6-0df4164bfeab",
	"nombre":"Sgt. Pepper's Lonely Heart Club Band",
	"tipo":"album"
}
```

##### Radio

```
/escuchas/radio?fechaStart=2020-05-01&fechaEnd=2020-07-01
```

```
{
	"id_ambito":"a98cf4a0-8df8-4b17-9c04-a7e54565e16d",
	"nombre":"Queen Radio",
	"tipo":"radio"
}
```

#### Discos y Radios de Artista

Para ver los discos y radios de un artista debemos proveer el id del artista:

* artistaID

```
/artista/ambitos?artistaID=70523e4c-fdec-4f23-b555-25ccd5e69eb6
```

```
[
	{"id_ambito":"eff0bfb7-1907-4b98-b093-3f5ca7fbe3a5",
	"nombre":"Abbey Road",
	"tipo":"album"},

	{"id_ambito":"c0524bb7-179a-47e5-a3b6-0df4164bfeab",
	"nombre":"Sgt. Pepper's Lonely Heart Club Band",
	"tipo":"album"}
]
```

#### Canciones de un Disco

Para ver las canciones de un disco debemos proveer el id del disco:

* ambitoID

```
/album/canciones?ambitoID=04a32389-930f-41d3-9d79-a62a0bfdc7c9
```


```
[
	{"id_cancion":"cc630a52-487e-434e-986e-93f65a0fb6eb",
	"nombre":"Nightrain"},
	{"id_cancion":"02094eac-18cc-413d-b3ce-2f0f6313d20e",
	"nombre":"Rocket Queen"},
	{"id_cancion":"f25b4b25-383d-494b-b75c-9117c10bf224",
	"nombre":"My Michelle"},
	{"id_cancion":"acf5f441-09d9-453a-939a-c574d8394c92",
	"nombre":"Welcome to the Jungle"},
	{"id_cancion":"336cf7ef-707c-4025-a949-587735de3302",
	"nombre":"Mr. Brownstone"}
]
```

#### Escuchas de Entidades

Para ver la cantidad de escuchas de cada entidad debemos proveer el id de la misma:

##### Cancion

* cancionID

```
/cancion/escuchas?cancionID=6b6f59fd-6646-426d-8b30-9e72eba85a9d
```

```
3
```

##### Album

* ambitoID

```
/album/escuchas?ambitoID=7e8fba26-d300-4e75-a13b-5053c994ea68
```

```
7
```

#### Datos de Usuario

Para ver la información de un usuario debemos proveer el id del mismo:

* userID

```
/user?userID=160829b0-133b-475d-9841-41e833bea705
```

```
{
	"id_user":"160829b0-133b-475d-9841-41e833bea705",
	"username":"jplagostena",
	"nombre":"Juan Pablo Lagostena"
}
```

#### Discovery

Para obtener un disco que no haya un escuchado de un artista que sí para un usuario debemos proveer el id del mismo:

* userID

```
/user/discovery?userID=92s2o26f-35ia-4s21-9t24-85asii08ly82
```

```
{
	"id_ambito":"eff0bfb7-1907-4b98-b093-3f5ca7fbe3a5",
	"nombre":"Abbey Road","tipo":"album"
}
```

### Salud

Podemos obtener métricas de salud para los siguientes servicios:

* sputnikfy
* queries-service
* music-service
* users-service
* statistics-service
* statistics-listener

Para hacerlo debemos consultar la siguiente ruta:

```
/actuator/health
```















