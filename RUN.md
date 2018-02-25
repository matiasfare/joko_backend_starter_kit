## Requisitos para ejecutar el servicio

* IDE compatible con proyectos MAVEN
* Java 8 (JDK8)
* Base de datos PostgreSQL con el nombre XXXXX

## Clonar proyecto

Debe clonar el proyecto de (Es un repositorio autenticado, consultar el URL para `clone` en):

```shell
$ git clone XXXXXX
$ cd XXXX
```

## Configuración
El proyecto posee un conjunto de scripts que nos permiten automatizar el ciclo
 de vida de la base de datos. Con esto se puede crear facilmente toda la BD 
 desde la linea de comandos. Para actualizar hay que seguir los siguientes 
 pasos:
  
 ### Step 1) Crear el directorio PROFILE_DIR
 El directorio de profile contiene el archivo application.properties con la 
 configuracion necesaria para lanzar la aplicacion spring-boot.
 
 La convencion utilizada es tener un directorio, dentro del cual existan 
 varios PROFILE_DIR segun se requiera. Por ejemplo:
 ```shell
 /opt/starter_kit/dev
 /opt/starter_kit/qa
 ```
 
 En el anterior ejemplo existen dos PROFILE_DIR dentro del joko-demo, el 
 primero para development y el segundo con datos de quality assurance.
 
 Para referirse al PROFILE_DIR como parametro se usa "ext.prop.dir".
 
 Obs.: Un archivo de ejemplo para el application.properties se encuentra en 
 `src/main/resources/application.properties.examples`
  
  ### Step 2) Configuración del archivo "development.vars"
  
  Se debe configurar el archivo "development.vars", que servirá para la 
  ejecucion de liquibase. Este es un archivo bash que debe tener dos variables: 
  
  - MVN_SETTINGS: Archivo de configuracion de perfil Maven. En caso de utilizar
   el Artifactory interno, sería el recien descargado. Ej. $HOME/.m2/settings.xml
  - PROFILE_DIR: Directorio de perfil creado en el punto inicial. Ej. 
  /opt/starter_kit/dev
  
  Un ejemplo de este archivo se encuenta en `src/main/resources/development.vars`.
  
  Se recomienda que este archivo esté fuera del workspsace en el directorio 
  padre de los PROFILE_DIR. Ejemplo: ``/opt/starter_kit/``.
  
  ### Step 3) Configuración de variables de entorno
  Exportar variable, desde la terminal:
  ```shell
    $ export ENV_VARS="/opt/starter_kit/development.vars"
  ```
  Obs.: El truco es tener varios archivos profile.vars y cada uno apuntando a
   un PROFILE_DIR diferente. 
   
  ### Step 4) Ejecutar Liquibase.
  
  1. Crea la schema de cero.
  ```shell
    $ ./scripts/updater fresh
  ```
  2. (Re)Inicializa datos básicos
  ```shell
    $ ./scripts/updater seed src/main/resources/db/sql/seed-data.sql
    $ ./scripts/updater seed src/main/resources/db/sql/seed-config.sql
  ```
  **OJO**:
    * El parámetro "fresh" elimina la base de datos que está configurada en el application.properties
      y la vuelve a crear desde cero con la última versión del schema
  
    * El parámetro "seed <file>" carga datos indicados en el archivo <file>, para los casos en que se
      ejecute "fresh" siempre debe ir seguido de un "seed" con el archivo que (re)inicializa los datos
      básicos del sistema 
  
    * Los datos básicos del sistema estan en dos archivos:
      ** seed-data.sql: Todos la configuracion base que es independiente al 
      ambiente
      ** [ambiente]-config. Por ejemplo: dev-config.sql . Posee los parametros 
      de configuracion adecuados  para el ambiente de
    desarrollo. Tambien existe qa-config y prod-config
  
  3. Para correr el liquibase en modo de actualización ejecute:
  ```shell
    $ ./scripts/updater update
  ```
  
  
  

## Corren con Maven

Una vez hechos estos cambios, solo debemos correr el proyecto como una 
aplicación de Spring Boot, o con la línea de comando (se requiere maven instalado).

```shell
  $ mvn spring-boot:run -Dext.prop.dir=/opt/starter_kit/dev -D 
  spring.config.location=file:///opt/starter_kit/dev/application.properties
```

STS
----
Para poder levantar la apliación desde un IDE, se debe añadir el parámetro 
como argumento de la VM '-Dspring.config.location=file://'
 con la ruta al archivo  application.properties. Por ejemplo en el IDE 
 Eclipse-STS  ir hasta debug/debug-configuracion,
 añadir una nueva configuración e ir hasta la pestaña (x)Arguments Luego 
 insertar el parámetro en el campo 'VM arguments'. Ejemplo:

    -Dspring.config.location=file:///opt/starter_kit/dev/application.properties -Dext
    .prop.dir=/opt/finhealth/dev


La mayoría de los IDEs soportan ejecución de aplicaciones tipo Spring Boot o 
permiten configurar ejecuciones customizadas de maven.

### Swagger API
El proyecto cuenta con documentación del API accesible desde el swagger-ui. URI al swagger:

    http://localhost:8080/swagger/index.html