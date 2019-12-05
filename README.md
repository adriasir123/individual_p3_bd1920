# TL;DR
En este repositorio se encuentra el código de mi parte individual para la práctica 3 de base de datos "Gestión de usuarios". Soy el alumno 1, y me corresponde hacer la parte de Oracle.
La documentación de cada ejercicio con pruebas de funcionamiento, las podrás encontrar en este mismo README

## Ejercicio 1
### Enunciado

Crea un rol ROLPRACTICA1 con los privilegios: 
- Conectarse a la base de datos
- Crear tablas y vistas
- Insertar datos en la tabla EMP de SCOTT.

### Realización

```
CREATE ROLE ROLPRACTICA1;
grant create session to ROLPRACTICA1;
grant create table to ROLPRACTICA1;
grant create view to ROLPRACTICA1;
grant insert on scott.emp to ROLPRACTICA1;
```
### Prueba de funcionamiento



## Ejercicio 2
Crea un usuario USRPRACTICA1 con el tablespace USERS por defecto
Averigua qué cuota se le ha asignado por defecto en el mismo
Sustitúyela por una cuota de 1M

```
create user USRPRACTICA1 identified by 1234 default tablespace users;  
  
Cuota por defecto en ese tablespace  
  
Sustituir por cuota de 1M
```


## Ejercicio 3
Modifica el usuario USRPRACTICA1 para que tenga cuota 0 en el tablespace SYSTEM.

---

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





## Ejercicio 14
### Enunciado

Consulta qué usuarios existen en tu base de datos

### Realización

```
SELECT USERNAME
FROM ALL_USERS;
```

### Prueba de funcionamiento








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

```



### Prueba de funcionamiento







## Ejercicio 22
Elige un perfil y consulta qué límites se establecen en el mismo.







## Ejercicio 23
Muestra los nombres de los usuarios que tienen limitado el número de sesiones concurrentes.










## Ejercicio 24
Realiza un procedimiento que reciba un nombre de usuario y un privilegio de sistema y nos muestre el mensaje 'SI, DIRECTO' si el usuario tiene ese privilegio concedido directamente, 'SI, POR ROL' si el usuario tiene ese privilegio en alguno de los roles que tiene concedidos y un 'NO' si el usuario no tiene dicho privilegio.











## Ejercicio 25
Realiza un procedimiento llamado _MostrarNumSesiones_ que reciba un nombre de usuario y muestre el número de sesiones concurrentes que puede tener abiertas como máximo y las que tiene abiertas realmente.








<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1MjM3NDIwNDMsMTM2MDg0MjI2MSwxOT
A1MDc4Nzg4LC0zNzM3OTY5MTEsMTE4NjkxNTI5OSwtOTk2Mjg2
NjA1LC0xNzY1MjQzMTc2LDE0MTk5MTk4NjUsNDQ3MzcwNzI5LD
IzMzAyNjc3MiwyMDkzMDM0MDUzLC0xMDYyMjE5NDY4LDE1OTU5
NzA0NjksMTc1OTE0Mjk4NSwtMTYxNTEyMjMyLC0xMzQyMjcxMz
k3LDEwMTgxNzE5MjEsMTY3NTY1MTM0MSw0Mjc2NjE2MTksLTEx
MjI4MzQ0NjddfQ==
-->