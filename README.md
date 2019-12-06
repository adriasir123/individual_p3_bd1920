# TL;DR
En este repositorio se encuentra el código de mi parte individual para la práctica 3 de base de datos "Gestión de usuarios". Soy el alumno 1, y me corresponde hacer la parte de Oracle. La documentación de cada ejercicio con pruebas de funcionamiento, las podrás encontrar en este mismo README

## Ejercicio 1 :heavy_check_mark:
### Enunciado

Crea un rol ROLPRACTICA1 con los privilegios: 
- Conectarse a la base de datos
- Crear tablas y vistas
- Insertar datos en la tabla EMP de SCOTT.

### Realización

```
create role ROLPRACTICA1;
grant create session to ROLPRACTICA1;
grant create table to ROLPRACTICA1;
grant create view to ROLPRACTICA1;
grant insert on scott.emp to ROLPRACTICA1;
```

### Prueba de funcionamiento
Para comprobar que los cambios hayan surtido efecto de verdad, tendremos que comprobar 2 cosas:
* Si el rol se ha creado
```
SELECT role 
	FROM DBA_ROLES
	WHERE role = 'ROLPRACTICA1';
```
```
ROLE
---------------
ROLPRACTICA1
```
_El rol se ha creado correctamente_

* Si ese rol tiene los privilegios que le hemos asignado. En este caso es sencillo, ya que casi todos privilegios que se le han asignado son de sistema. El único privilegio sobre objeto que se le ha concedido es el de insert. Por lo tanto, tenemos que consultar las vistas DBA_SYS_PRIVS y DBA_TAB_PRIVS
```
select PRIVILEGE
	from DBA_SYS_PRIVS
	where grantee = 'ROLPRACTICA1';
```
```
PRIVILEGE
-------------------
CREATE TABLE
CREATE VIEW
CREATE SESSION
```
```
select PRIVILEGE
	from DBA_TAB_PRIVS
	where grantee = 'ROLPRACTICA1';
```
```
PRIVILEGE
----------
INSERT
```
_Se han listado correctamente todos los privilegios que previamente le habíamos asignado. Ha funcionado correctamente_





## Ejercicio 2 :warning:
### Enunciado

Crea un usuario USRPRACTICA1 con el tablespace USERS por defecto
Averigua qué cuota se le ha asignado por defecto en el mismo
Sustitúyela por una cuota de 1M

### Realización
* Creamos el usuario con el tablespace dicho
```
create user USRPRACTICA1 identified by 1234 default tablespace users;  
```
* Comprobamos que esto se ha hecho correctamente
```
select username, DEFAULT_TABLESPACE
	from DBA_USERS
	where username = 'USRPRACTICA1';
```
Output :arrow_double_down:
```
USERNAME                                                                                                                 DEFAULT_TABLESPACE
-------------------------------------------------------------------------------------------------------------------------------- ------------
USRPRACTICA1                                                                                                             USERS
```
* Mostramos su cuota sobre users
```
select tablespace_name, username, max_bytes
	from dba_ts_quotas
	where username = 'USRPRACTICA1' and TABLESPACE_NAME = 'USERS';
```
```
no rows selected
```
No muestra ningún registro porque por defecto en Oracle la cuota de los usuarios sobre los tablespaces es de 0. Si queremos que explícitamente tengan cuota (limitada o ilimitada), tendremos que asignársela.
Encontré lo siguiente en la documentación de oracle, de donde saqué la información para haber sabido esto
>By default, a user has no quota on any tablespace in the database. If the user has the privilege to create a schema object, you must assign a quota to allow the user to create objects. Minimally, assign users a quota for the default tablespace, and additional quotas for other tablespaces in which they can create objects.

Por lo tanto, en cuanto le asignemos una quota sobre un tablespace, ya aparecería con la consulta anterior. Es lo que haremos a continuación
* Cambiarle la cuota asignada
```
alter user USRPRACTICA1 quota 1M on USERS; 
```
Ahora SÍ podremos mostrar la cuota asignada para ese usuario, en el tablespace dicho:
```

```



## Ejercicio 3
### Enunciado

Modifica el usuario USRPRACTICA1 para que tenga cuota 0 en el tablespace SYSTEM

### Realización
```
alter user USRPRACTICA1 quota 0 on SYSTEM;
```




## Ejercicio 4
Concede a USRPRACTICA1 el ROLPRACTICA1.

---

```
GRANT ROLPRACTICA1 TO USRPRACTICA1;
```


## Ejercicio 5
### Enunciado

Concede a USRPRACTICA1 los privilegios:
- Crear tablas en el esquema de cualquier usuario
- Insertar datos en el esquema de cualquier usuario
Probar los privilegios
Comprueba si puede modificar la estructura o eliminar las tablas creadas.

### Realización
```
grant create any table to USRPRACTICA1;
grant INSERT ANY TABLE to USRPRACTICA1;
```

### Prueba de funcionamiento



## Ejercicio 6
### Enunciado

Concede a USRPRACTICA1 el privilegio de leer la tabla DEPT de SCOTT con la posibilidad de que lo pase a su vez a terceros usuarios.

### Realización

```
grant read on scott.dept to USRPRACTICA1 with grant option; 
```

### Prueba de funcionamiento


## Ejercicio 7
### Enunciado

Comprueba que USRPRACTICA1 puede realizar todas las operaciones previstas en el rol (se refiere al rol que le dimos antes, ROLPRACTICA1).

### Realización
Recordatorio de lo que hacía el rol
```
CREATE ROLE ROLPRACTICA1;
Grant Create session to ROLPRACTICA1;
Grant Create table to ROLPRACTICA1;
Grant Create view to ROLPRACTICA1;
grant insert on scott.emp to ROLPRACTICA1;
```

### Prueba de funcionamiento



## Ejercicio 8
### Enunciado

Quita a USRPRACTICA1 el privilegio de crear vistas. Comprueba que ya no puede hacerlo.

### Realización


```
REVOKE CREATE VIEW FROM USRPRACTICA1;
```


### Prueba de funcionamiento




## Ejercicio 9
### Enunciado

Crea un perfil NOPARESDECURRAR que limita a dos el número de minutos de inactividad permitidos en una sesión.


### Realización

```
CREATE PROFILE NOPARESDECURRAR LIMIT IDLE_TIME 2;
```



### Prueba de funcionamiento





## Ejercicio 10
### Enunciado

Activa el uso de perfiles en ORACLE

### Realización
Profiles only take effect when resource limits are "turned on" for the database as a whole. 
Es por eso, que debemos ejecutar:
```
ALTER SYSTEM SET RESOURCE_LIMIT=TRUE
```

Se puede comprobar el estado de éste parámetro con 
```
show parameter resource_limit
```


### Prueba de funcionamiento




## Ejercicio 11
### Enunciado

Asigna el perfil creado a USRPRACTICA1 y comprueba su correcto funcionamiento.

### Realización

```
ALTER USER USRPRACTICA1 PROFILE NOPARESDECURRAR;
```

### Prueba de funcionamiento



## Ejercicio 12
### Enunciado

Crea un perfil CONTRASEÑASEGURA especificando que:
- La contraseña caduque mensualmente
- Sólo se permitan tres intentos fallidos para acceder a la cuenta
- En caso de superarse los 3 intentos, la cuenta se bloqueará indefinidamente


### Realización

```
CREATE PROFILE CONTRASEÑASEGURA LIMIT
	PASSWORD_LIFE_TIME 30
	FAILED_LOGIN_ATTEMPTS 4
	PASSWORD_LOCK_TIME UNLIMITED
;
```


### Prueba de funcionamiento






## Ejercicio 13
### Enunciado

Asigna el perfil creado a USRPRACTICA1 y comprueba su funcionamiento
Desbloquea posteriormente al usuario (porque el perfil hace que al 4 error de introducción de contraseña, bloquee al usuario)

### Realización

```
ALTER USER USRPRACTICA1 PROFILE CONTRASEÑASEGURA;
ALTER USER USRPRACTICA1 IDENTIFIED BY 1234 ACCOUNT UNLOCK;
```

### Prueba de funcionamiento





## Ejercicio 14 :heavy_check_mark:
### Enunciado

Consulta qué usuarios existen en tu base de datos

### Realización

```
SELECT USERNAME
FROM DBA_USERS;
```

### Prueba de funcionamiento

```
USERNAME
-----------------------
SYS
SYSTEM
XS$NULL
OJVMSYS
LBACSYS
OUTLN
SYS$UMF
DBSNMP
APPQOSSYS
DBSFWUSER
GGSYS
ANONYMOUS
CTXSYS
SI_INFORMTN_SCHEMA
DVSYS
DVF
GSMADMIN_INTERNAL
ORDPLUGINS
MDSYS
OLAPSYS
ORDDATA
XDB
WMSYS
ORDSYS
GSMCATUSER
MDDATA
SYSBACKUP
REMOTE_SCHEDULER_AGENT
GSMUSER
SYSRAC
AUDSYS
DIP
SYSKM
ORACLE_OCM
SCOTT
SYSDG
SPATIAL_CSW_ADMIN_USR

37 rows selected.
```


## Ejercicio 15
### Enunciado

Elige un usuario concreto y consulta qué cuota tiene sobre cada uno de los tablespaces.


### Realización
Voy a hacerlo sobre AUDSYS
```
select tablespace_name, username, bytes, max_bytes
	from dba_ts_quotas
	where username = 'AUDSYS';
```


### Prueba de funcionamiento








## Ejercicio 16
### Enunciado

Elige un usuario concreto y muestra qué privilegios de sistema tiene asignados.

### Realización

```
SELECT * FROM DBA_SYS_PRIVS  
Where grantee = system;
```

### Prueba de funcionamiento







## Ejercicio 17
### Enunciado

Elige un usuario concreto y muestra qué privilegios sobre objetos tiene asignados.

### Realización

```
SELECT * FROM DBA_TAB_PRIVS  
Where grantee = system;
```


### Prueba de funcionamiento







## Ejercicio 18
### Enunciado

Consulta qué roles existen en tu base de datos.

### Realización

```
SELECT role FROM DBA_ROLES;
```


### Prueba de funcionamiento




## Ejercicio 19
### Enunciado

Elige un rol concreto y consulta qué usuarios lo tienen asignado.

### Realización

```
SELECT granted_role, username FROM USER_ROLE_PRIVS  
WHERE granted_role = <nombre>;
```

### Prueba de funcionamiento





## Ejercicio 20
### Enunciado

Elige un rol concreto y averigua si está compuesto por otros roles o no

### Realización

```
Select * from  
DBA_ROLE_PRIVS  
Where grantee = nombre_rol;
```


### Prueba de funcionamiento




## Ejercicio 21
### Enunciado

Consulta qué perfiles existen en tu base de datos.

### Realización

```
SELECT profile  
From dba_profiles;
```



### Prueba de funcionamiento







## Ejercicio 22
### Enunciado

Elige un perfil y consulta qué límites se establecen en el mismo.

### Realización

```
SELECT limit  
From dba_profiles  
Where profile = nombre_perfil;
```



### Prueba de funcionamiento





## Ejercicio 23
### Enunciado

Muestra los nombres de los usuarios que tienen limitado el número de sesiones concurrentes.

### Realización

```
SELECT USERNAME, PROFILE  
FROM DBA_USERS  
Where profile = (  
  
SELECT distinct profile  
From dba_profiles  
Where limit = sessions_per_user;  
)
```

### Prueba de funcionamiento







## Ejercicio 24 :x:
Realiza un procedimiento que reciba un nombre de usuario y un privilegio de sistema y nos muestre el mensaje 'SI, DIRECTO' si el usuario tiene ese privilegio concedido directamente, 'SI, POR ROL' si el usuario tiene ese privilegio en alguno de los roles que tiene concedidos y un 'NO' si el usuario no tiene dicho privilegio.











## Ejercicio 25
Realiza un procedimiento llamado _MostrarNumSesiones_ que reciba un nombre de usuario y muestre el número de sesiones concurrentes que puede tener abiertas como máximo y las que tiene abiertas realmente.








<!--stackedit_data:
eyJoaXN0b3J5IjpbLTY0MDM2NDExLC0xMjE2ODg2MDAwLDk0MD
QyMjM2MiwtMTAxNzI3ODY2NCwtMTU3NDA3NjcsOTg3NjU0ODY1
LC05MTU5MjUwNjQsMTM4NTg4MjAyMSwtNjkxMzYzMTcyLDE5Nj
M5NjQ5NDIsLTE5ODY4NDg4NTksLTEyMjgzODQ5ODksLTE0NTM3
NzkxMTAsLTkzMDU5NDg4NSwxNzUyMzA3MzY5LC0xOTUyODQ3Mj
A2LC01NTI3MDMzMDEsNzY0NjgxODIwLC05OTg4MDgzNDQsLTEz
NzkyMTE4NTRdfQ==
-->