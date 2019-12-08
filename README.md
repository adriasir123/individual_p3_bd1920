
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





## Ejercicio 2 :heavy_check_mark:
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
No muestra ningún registro porque por defecto en Oracle la cuota de los usuarios sobre los tablespaces es de 0. Si queremos que explícitamente tengan cuota (limitada o ilimitada, en éste último caso aparecería con el valor -1), tendremos que asignársela.
Encontré lo siguiente en la documentación de oracle, de donde saqué la información para haber sabido esto
>By default, a user has no quota on any tablespace in the database. If the user has the privilege to create a schema object, you must assign a quota to allow the user to create objects. Minimally, assign users a quota for the default tablespace, and additional quotas for other tablespaces in which they can create objects.

Por lo tanto, en cuanto le asignemos una quota sobre un tablespace, ya aparecería con la consulta anterior. Es lo que haremos en el siguiente punto.

> NOTA IMPORTANTE: por curiosidad quise saber si cuando a un usuario no se le asigna cuota en un tablespace por defecto, tiene realmente cuota o no. Porque con este usuario, que en principio no tenía cuota, pude crear una tabla, y esto me confundió bastante, sabiendo que desde la documentación oficial de oracle decían que no tienen cuota si no se la asignas. Creé [esta pregunta](https://dba.stackexchange.com/questions/254949/what-is-the-default-quota-on-a-tablespace-for-a-given-user-in-oracle-if-you-don/254950#254950)  en stack exchange para que me la resolvieran, y llegué a la conclusión de que aunque hubiese podido crear tablas, realmente no se estaban guardando en el tablespace users. Es decir, es real que ese usuario no tenía cuota sobre el tablespace.
La respuesta en resumen fue que pude crear tablas debido al "**deferred_segment_creation**".
Indagando un poco más sobre esto vi los conceptos de segmento, partición...etc. Pero creo que esto se escapa del ámbito de este tema, y seguramente es lo que vayamos a dar en el tema del almacenamiento. 
Aún así, creo que ha sido muy interesante investigar esto.

* Cambiarle la cuota asignada
```
alter user USRPRACTICA1 quota 1M on USERS; 
```
Ahora SÍ podremos mostrar la cuota asignada para ese usuario, en el tablespace dicho:  
```
SQL> select tablespace_name, username, max_bytes
  2     from dba_ts_quotas
  3     where username = 'USRPRACTICA1' and TABLESPACE_NAME = 'USERS';
TABLE USERNAME      MAX_BYTES
----- ------------ ----------
USERS USRPRACTICA1    1048576
```


## Ejercicio 3 :heavy_check_mark:
### Enunciado

Modifica el usuario USRPRACTICA1 para que tenga cuota 0 en el tablespace SYSTEM

### Realización
* Le modificamos la cuota
```
alter user USRPRACTICA1 quota 0 on SYSTEM;
```
* Comprobamos que se han realizado los cambios
```
select tablespace_name, username, max_bytes
	from dba_ts_quotas
	where username = 'USRPRACTICA1' and TABLESPACE_NAME = 'SYSTEM';
```
Output :arrow_double_down:
```
no rows selected
```
_Esto es correcto porque como vimos antes, cualquier usuario con cuota 0 sobre un tablespace, no aparecerá en esa vista_


## Ejercicio 4 :heavy_check_mark:
### Enunciado

Concede a USRPRACTICA1 el ROLPRACTICA1

### Realización
* Le asignamos el rol dicho
```
GRANT ROLPRACTICA1 TO USRPRACTICA1;
```
```
Grant succeeded.
```
* Comprobamos que este cambio se haya registrado
```
select granted_role, grantee
	from DBA_ROLE_PRIVS
	where granted_role = 'ROLPRACTICA1' AND grantee = 'USRPRACTICA1';
```
```
GRANTED_ROLE GRANTEE
------------ ------------------
ROLPRACTICA1 USRPRACTICA1
```
_Ha aparecido el resultado en esa vista, por lo tanto el rol se concedió correctamente_



## Ejercicio 5 :heavy_check_mark:
### Enunciado

Concede a USRPRACTICA1 los privilegios:
- Crear tablas en el esquema de cualquier usuario
- Insertar datos en el esquema de cualquier usuario

Probamos los privilegios
Comprobamos si puede modificar la estructura o eliminar las tablas creadas.

### Realización
* Primero, concedemos los privilegios dichos
```
grant create any table to USRPRACTICA1;
grant insert any table to USRPRACTICA1;
```
* Para probar estos privilegios, en mi caso por ejemplo he creado un usuario "USRPRACTICA2"
```
create user USRPRACTICA2 identified by 1234 default tablespace users quota 1M on users;  
```
> Es importante que al crear este usuario le asignemos previamente una cuota, ya que cuando creemos una tabla e insertemos datos sobre ella desde el otro usuario, nos va a dar un error de cuota.
> ¿Por qué da este error de cuota? Muy sencillo, es porque cuando realizamos acciones en el esquema de otro usuario, lo hacemos a nombre de ese usuario, y ese mismo debe de poder tener cuota para crear lo que sea que queramos. 
> Por ejemplo, si estamos queriendo crear una tabla, cuando se cree, tendrá como owner "USRPRACTICA2" y no "USRPRACTICA1".
> Si creásemos en primer lugar el usuario "USRPRACTICA2" sin cuota sobre users, parece ser que desde un usuario externo, no entra en funcionamiento el "deferred_segment_creation", porque al intentar crear una tabla, directamente da el error de cuota.

Ahora, en el esquema del usuario USRPRACTICA2, conectado como USRPRACTICA1, crearé una tabla simple, y le añadiré un registro
```
CREATE TABLE USRPRACTICA2.EMP
(EMPNO NUMBER(4) NOT NULL,
ENAME VARCHAR2(10),
JOB VARCHAR2(9),
MGR NUMBER(4),
HIREDATE DATE,
SAL NUMBER(7, 2),
COMM NUMBER(7, 2),
DEPTNO NUMBER(2));

INSERT INTO USRPRACTICA2.EMP (empno, ename)
VALUES (1, 'paco');
```
Para comprobar que la tabla se creó correctamente y tiene el registro, me conectaré de nuevo con nuevo permisos de dba. ¿Por qué? Pues porque ahora mismo USRPRACTICA1 no tiene permisos de select sobre nada del esquema de USRPRACTICA2, y éste último, tampoco tiene permisos de create session.
Así que, pasaré a mostrar los datos de esa tabla:
```
select * from usrpractica2.emp;
```
```
     EMPNO ENAME      JOB              MGR HIREDATE        SAL       COMM     DEPTNO
---------- ---------- --------- ---------- -------- ---------- ---------- ----------
         1 paco
```
_Tanto los campos de la tabla como el registro añadido son correctos, así que todos los pasos se realizaron correctamente_

* Para terminar, tendremos que comprobar si USRPRACTICA1 puede modificar la estructura de la tabla que acaba de crear, por ejemplo. Para llevar a cabo esta acción, serviría el eliminar una columna de esa tabla, por lo que intentaré borrar la columna "JOB"
```
ALTER TABLE USRPRACTICA2.EMP DROP COLUMN JOB;
```
```
ALTER TABLE USRPRACTICA2.EMP DROP COLUMN JOB
*
ERROR en lÝnea 1:
ORA-01031: privilegios insuficientes
```
Evidentemente USRPRACTICA1 no puede realizar esta acción, ya que no posee los permisos necesarios. Para poder hacer esto, tendría que tener permisos de `ALTER` sobre esa tabla específicamente, o permisos de `ALTER ANY TABLE`.

Además, también tenemos que comprobar si USRPRACTICA1 puede borrar la tabla creada. Para ello, ejecutamos lo siguiente
```
DROP TABLE usrpractica2.emp;
```
```
DROP TABLE usrpractica2.emp
                        *
ERROR en lÝnea 1:
ORA-01031: privilegios insuficientes
```
De nuevo, volvemos a tener errores de permisos. Así que, ¿qué necesitaríamos para poder borrar tablas en otros esquemas? Pues hay 2 opciones:
* Ser el owner de la tabla 
* Tener el privilegio `DROP ANY TABLE`








## Ejercicio 6 :heavy_check_mark:
### Enunciado

Concede a USRPRACTICA1 el privilegio de leer la tabla DEPT de SCOTT, con la posibilidad de que lo pase a su vez a terceros usuarios

### Realización
* Concedemos el privilegio dicho
```
grant read on scott.dept to USRPRACTICA1 with grant option; 
```
> El privilegio "read" se añadió a partir de la versión de Oracle 12c, como mejora para la seguridad de la base de datos. Éste permiso, reemplaza al clásico permiso de "select".
> Se recomienda el uso de "read" para usuarios de sólo lectura, ya que evita la posibilidad de ataques DoS
* A continuación, probaremos el privilegio haciendo una simple consulta a DEPT desde USRPRACTICA1
```
select * from scott.dept;
```
```
    DEPTNO DNAME          LOC
---------- -------------- -------------
        10 ACCOUNTING     NEW YORK
        20 RESEARCH       DALLAS
        30 SALES          CHICAGO
        40 OPERATIONS     BOSTON
```
_Ha funcionado correctamente_
* Lo siguiente que vamos a probar es si USRPRACTICA1 puede pasarle este permiso a USRPRACTICA2. Y claramente, tendremos que probar si éste último puede hacer la misma consulta que hizo USRPRACTICA1.
Le pasamos el permiso
```
grant read on scott.dept to USRPRACTICA2;
```
```
Grant succeeded.
```
> Como vemos, le he pasado el permiso de manera "normal". Es decir, no tiene el "with grant option", por lo tanto USRPRACTICA2 no podría pasar este privilegio a terceros. 
> Aún así, se podría perfectamente pasar el permiso con "with grant option", dándo esa posibilidad extra para pasarlo a terceros.

Bien, el primer paso ha funcionado. Ahora veamos si podemos realmente leer la tabla desde USRPRACTICA2
```
SQL> connect usrpractica2;
Introduzca la contrase±a:
Conectado.
SQL> ALTER SESSION SET NLS_LANGUAGE=ENGLISH;

Session altered.

SQL> select * from scott.dept;

    DEPTNO DNAME          LOC
---------- -------------- -------------
        10 ACCOUNTING     NEW YORK
        20 RESEARCH       DALLAS
        30 SALES          CHICAGO
        40 OPERATIONS     BOSTON

SQL>
```
_Como podemos comprobar, ha funcionado correctamente. He mostrado el prompt completo desde que realicé la conexión con USRPRACTICA2, para que se vea que de verdad lo he hecho_



## Ejercicio 7 :heavy_check_mark:
### Enunciado

Comprueba que USRPRACTICA1 puede realizar todas las operaciones previstas en el rol (nos referimos al rol que le dimos antes, ROLPRACTICA1)

### Realización
_Recordatorio de lo que permitía hacer ROLPRACTICA1_
```
grant create session to ROLPRACTICA1;
grant create table to ROLPRACTICA1;
grant create view to ROLPRACTICA1;
grant insert on scott.emp to ROLPRACTICA1;
```
Así que, vamos a probar cada una de estas acciones con USRPRACTICA1

* Abrimos conexión
```
connect usrpractica1;
```
```
Introduzca la contrase±a:
Conectado.
```
_Esto es lo que se muestra cuando hemos conseguido abrir una conexión correctamente_
* Creamos una tabla
```
CREATE TABLE EMP
(EMPNO NUMBER(4) NOT NULL,
ENAME VARCHAR2(10),
JOB VARCHAR2(9),
MGR NUMBER(4),
HIREDATE DATE,
SAL NUMBER(7, 2),
COMM NUMBER(7, 2),
DEPTNO NUMBER(2));
```
```
Table created.
```
_La tabla se creó correctamente_

* Creamos una vista. Ya que previamente le habíamos dado permiso de lectura a USRPRACTICA1 sobre la tabla DEPT de SCOTT, aprovecharemos esta información para crearla
```
CREATE VIEW view1 AS
  SELECT *
  FROM scott.dept;
```
```
View created.
```
Una vez hecho esto, comprobaremos si los datos de la vista creada son los correctos
```
SQL> select * from view1;

    DEPTNO DNAME          LOC
---------- -------------- -------------
        10 ACCOUNTING     NEW YORK
        20 RESEARCH       DALLAS
        30 SALES          CHICAGO
        40 OPERATIONS     BOSTON
```
_Efectivamente, la vista se ha creado con los datos correctos_

* Por último, vamos a probar si podemos insertar registros en la tabla EMP de SCOTT
```
INSERT INTO SCOTT.EMP VALUES
(8000, 'PAQUITO', 'CLERK', 7902,
TO_DATE('17-DEC-1980', 'DD-MON-YYYY'), 800, NULL, 20);
```
```
1 row created.
```
_Se insertó el registro correctamente_

Ahora, con un usuario que tenga permisos de dba (porque USRPRACTICA1 no tiene permisos de lectura sobre esa tabla), podremos ver el nuevo contenido de la tabla
```
SQL> select * from scott.emp;

     EMPNO ENAME      JOB              MGR HIREDATE        SAL       COMM     DEPTNO
---------- ---------- --------- ---------- -------- ---------- ---------- ----------
      8000 PAQUITO    CLERK           7902 17/12/80        800                    20
      7499 ALLEN      SALESMAN        7698 20/02/81       1600        300         30
      7521 WARD       SALESMAN        7698 22/02/81       1250        500         30
      7654 MARTIN     SALESMAN        7698 28/09/81       1250       1400         30
      7698 BLAKE      MANAGER         7839 01/05/81       2850                    30
      7782 CLARK      MANAGER         7839 09/06/81       2450                    10
      7839 KING       PRESIDENT            17/11/81       5000                    10
      7844 TURNER     SALESMAN        7698 08/09/81       1500          0         30

8 filas seleccionadas.
```
_Ha aparecido el nuevo registro que le hemos añadido, por lo tanto ha funcionado correctamente_


**TODAS LAS ACCIONES PREVISTAS PARA ROLPRACTICA1 HAN FUNCIONADO CORRECTAMENTE**







## Ejercicio 8 :heavy_check_mark:
### Enunciado

Quita a USRPRACTICA1 el privilegio de crear vistas. Comprueba que ya no puede hacerlo

### Realización
* Para conseguir esto, tendremos que quitarle el privilegio a ROLPRACTICA1, que es la razón real por la que USRPRACTICA1 puede crear vistas. Si intentamos quitarle el privilegio a USRPRACTICA1, no funcionará, porque oracle no permite quitar privilegios que no se han dado
```
REVOKE CREATE VIEW FROM ROLPRACTICA1;
```
```
Revoke succeeded.
```
_El privilegio se quitó correctamente_

* Ahora, probaremos a crear una segunda vista, y veremos si puede seguir creando vistas o no
```
CREATE VIEW view2 AS
  SELECT *
  FROM scott.dept;
```
```
CREATE VIEW view2 AS
            *
ERROR at line 1:
ORA-01031: insufficient privileges
```
_Como vemos, ya no podemos crear vistas_








## Ejercicio 9 :heavy_check_mark:
### Enunciado

Crea un perfil NOPARESDECURRAR, que limite a 2 el número de minutos de inactividad permitidos en una sesión

### Realización
* Creamos el perfil
```
CREATE PROFILE NOPARESDECURRAR LIMIT IDLE_TIME 2;
```
```
Profile created.
```
* Listamos el perfil para comprobar que se creó correctamente
```
select profile, resource_name, resource_type, limit
	from DBA_PROFILES
	where profile = 'NOPARESDECURRAR';
```
```
PROFILE                                                                                                                  RESOURCE_NAME                    RESOURCE LIMIT
-------------------------------------------------------------------------------------------------------------------------------- -------------------------------- --------
NOPARESDECURRAR                                                                                                          COMPOSITE_LIMIT                  KERNEL   DEFAULT
NOPARESDECURRAR                                                                                                          SESSIONS_PER_USER                KERNEL   DEFAULT
NOPARESDECURRAR                                                                                                          CPU_PER_SESSION                  KERNEL   DEFAULT
NOPARESDECURRAR                                                                                                          CPU_PER_CALL                     KERNEL   DEFAULT
NOPARESDECURRAR                                                                                                          LOGICAL_READS_PER_SESSION        KERNEL   DEFAULT
NOPARESDECURRAR                                                                                                          LOGICAL_READS_PER_CALL           KERNEL   DEFAULT
NOPARESDECURRAR                                                                                                          IDLE_TIME                        KERNEL   2
NOPARESDECURRAR                                                                                                          CONNECT_TIME                     KERNEL   DEFAULT
NOPARESDECURRAR                                                                                                          PRIVATE_SGA                      KERNEL   DEFAULT
NOPARESDECURRAR                                                                                                          FAILED_LOGIN_ATTEMPTS            PASSWORD DEFAULT
NOPARESDECURRAR                                                                                                          PASSWORD_LIFE_TIME               PASSWORD DEFAULT
NOPARESDECURRAR                                                                                                          PASSWORD_REUSE_TIME              PASSWORD DEFAULT
NOPARESDECURRAR                                                                                                          PASSWORD_REUSE_MAX               PASSWORD DEFAULT
NOPARESDECURRAR                                                                                                          PASSWORD_VERIFY_FUNCTION         PASSWORD DEFAULT
NOPARESDECURRAR                                                                                                          PASSWORD_LOCK_TIME               PASSWORD DEFAULT
NOPARESDECURRAR                                                                                                          PASSWORD_GRACE_TIME              PASSWORD DEFAULT
NOPARESDECURRAR                                                                                                          INACTIVE_ACCOUNT_TIME            PASSWORD DEFAULT

17 rows selected.
```
_Vemos que el perfil se ha creado correctamente, y que además oracle, aunque NO se lo indiquemos, añade unos límites por defecto por perfil creado_






## Ejercicio 10 :heavy_check_mark:
### Enunciado

Activa el uso de perfiles en ORACLE

### Realización
Extracto sacado de la documentación de oracle:
> Profiles only take effect when resource limits are "turned on" for the database as a whole. 

Es por eso, que debemos ejecutar lo siguiente para que los perfiles entren en funcionamiento
```
ALTER SYSTEM SET RESOURCE_LIMIT=TRUE;
```
```
System altered.
```
_Ha surtido efecto el cambio correctamente_

Luego, podemos comprobar el estado de éste parámetro con 
```
show parameter resource_limit;
```
```
NAME                                 TYPE        VALUE
------------------------------------ ----------- ------------------------------
resource_limit                       boolean     TRUE
```
Como podemos ver el "resource_limit" está activado, y a partir de ahora los perfiles de oracle estarán funcionando







## Ejercicio 11 :heavy_check_mark:
### Enunciado

Asigna el perfil creado a USRPRACTICA1 y comprueba su correcto funcionamiento.

### Realización
* Asignamos el perfil
```
ALTER USER USRPRACTICA1 PROFILE NOPARESDECURRAR;
```
```
User altered.
```
* Esperamos 2 minutos, y comprobamos que haya funcionado
```
ORA-02396: ha excedido el tiempo mßximo de inactividad, vuelva a conectarse
```
> NOTA IMPORTANTE: en la consola cuando se corta la conexión no nos aparece directamente el aviso. Es cuando intentamos realizar alguna acción, que nos avisa de que nos ha cortado la conexión



## Ejercicio 12
### Enunciado

Crea un perfil CONTRASEÑASEGURA especificando que:
- La contraseña caduque mensualmente
- Sólo se permitan tres intentos fallidos para acceder a la cuenta
- En caso de superarse los 3 intentos, la cuenta se bloqueará indefinidamente


### Realización
* Creamos el perfil con sus límites requeridos
```
CREATE PROFILE CONTRASENASEGURA LIMIT
	PASSWORD_LIFE_TIME 30
	FAILED_LOGIN_ATTEMPTS 4
	PASSWORD_LOCK_TIME UNLIMITED;
```
```
Profile created.
```
* Mostramos que se ha creado el perfil, y tiene los límites indicados
```
select profile, resource_name, limit
	from DBA_PROFILES
	where
		profile = 'CONTRASENASEGURA'
		AND
		(resource_name = 'PASSWORD_LIFE_TIME' OR
		resource_name = 'FAILED_LOGIN_ATTEMPTS' OR
		resource_name = ');
```





## Ejercicio 13
### Enunciado

Asigna el perfil creado a USRPRACTICA1 y comprueba su funcionamiento
Desbloquea posteriormente al usuario (porque el perfil hace que al 4 error de introducción de contraseña, bloquee al usuario)

### Realización

```
ALTER USER USRPRACTICA1 PROFILE CONTRASEÑASEGURA;
ALTER USER USRPRACTICA1 IDENTIFIED BY 1234 ACCOUNT UNLOCK;
```







## Ejercicio 14 :heavy_check_mark:
### Enunciado

Consulta qué usuarios existen en tu base de datos

### Realización
* Hacemos la consulta dicha
```
SELECT USERNAME
FROM DBA_USERS;
```
Su output es el siguiente
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
_La consulta ha funcionado correctamente_




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











## Ejercicio 16
### Enunciado

Elige un usuario concreto y muestra qué privilegios de sistema tiene asignados.

### Realización

```
SELECT * FROM DBA_SYS_PRIVS  
Where grantee = system;
```









## Ejercicio 17
### Enunciado

Elige un usuario concreto y muestra qué privilegios sobre objetos tiene asignados.

### Realización

```
SELECT * FROM DBA_TAB_PRIVS  
Where grantee = system;
```










## Ejercicio 18
### Enunciado

Consulta qué roles existen en tu base de datos.

### Realización

```
SELECT role FROM DBA_ROLES;
```







## Ejercicio 19
### Enunciado

Elige un rol concreto y consulta qué usuarios lo tienen asignado.

### Realización

```
SELECT granted_role, username FROM USER_ROLE_PRIVS  
WHERE granted_role = <nombre>;
```







## Ejercicio 20
### Enunciado

Elige un rol concreto y averigua si está compuesto por otros roles o no

### Realización

```
Select * from  
DBA_ROLE_PRIVS  
Where grantee = nombre_rol;
```







## Ejercicio 21
### Enunciado

Consulta qué perfiles existen en tu base de datos.

### Realización

```
SELECT profile  
From dba_profiles;
```











## Ejercicio 22
### Enunciado

Elige un perfil y consulta qué límites se establecen en el mismo.

### Realización

```
SELECT limit  
From dba_profiles  
Where profile = nombre_perfil;
```









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









## Ejercicio 24
Realiza un procedimiento que reciba un nombre de usuario y un privilegio de sistema y nos muestre el mensaje 'SI, DIRECTO' si el usuario tiene ese privilegio concedido directamente, 'SI, POR ROL' si el usuario tiene ese privilegio en alguno de los roles que tiene concedidos y un 'NO' si el usuario no tiene dicho privilegio.











## Ejercicio 25 :heavy_check_mark:
### Enunciado

Realiza un procedimiento llamado _MostrarNumSesiones_ que:
* Reciba un nombre de usuario como parámetro
* Muestre el número de sesiones concurrentes que puede tener abiertas como máximo y las que tiene abiertas realmente


### Realización
* Importante hacer esto al princicpio para que se nos muestren los mensajes
```
set serveroutput on;
```
* El código del procedimiento es el siguiente
```
CREATE OR REPLACE PROCEDURE MostrarNumSesiones (usuarioentrante IN VARCHAR2) 
IS
v_perfilsacado	VARCHAR2(30);
v_limitmaxsesiones	VARCHAR2(40);
v_numsesiones	NUMBER;

BEGIN
	-- Sacamos el perfil del usuario solicitado
	SELECT PROFILE
		INTO v_perfilsacado
		FROM DBA_USERS
		where username = usuarioentrante;

	-- Sacamos el número máximo de sesiones permitidas para ese usuario
	select limit
		INTO v_limitmaxsesiones
		from DBA_PROFILES
		where profile = v_perfilsacado AND RESOURCE_NAME = 'SESSIONS_PER_USER';

	-- Sacamos las sesiones abiertas por ese usuario actualmente
	select count(username)
		INTO v_numsesiones
		from V$session
		where username =usuarioentrante;


	dbms_output.put_line('El usuario '|| usuarioentrante || ' tiene un límite de sesiones concurrentes permitidas de: ' || v_limitmaxsesiones);
	dbms_output.put_line('Pero el número real de sesiones que tiene abiertas son: '|| v_numsesiones);
END;
/
```
* Como deberíamos ejecutar el procedimiento es así
```
EXEC MostrarNumSesiones (‘nombre_usuario’);
```

### Prueba de funcionamiento
Para probarlo, abriré 2 ventanas de SQL*PLUS con la sesión iniciada para el usuario USRPRACTICA1

Habiendo hecho esto, ejecutaré el procedimiento y mostraré su output
```
SQL> EXEC MostrarNumSesiones ('USRPRACTICA1');
El usuario USRPRACTICA1 tiene un límite de sesiones concurrentes permitidas de: UNLIMITED
Pero el número real de sesiones que tiene abiertas son: 2

PL/SQL procedure successfully completed.

SQL>
```
_Como vemos, todo ha funcionado como se esperaba_
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTAwNjIxMDk4OSwxNDcxNDE3NTQ1LC0xMT
M3MjI3NzM4LC0yMTA3Mzg3NjU0LDEzODA5NjUzMDUsMTIyMzU5
NTc1NiwyNzYwMzY2ODgsLTY3NzI3MjcxMCwtMTI4MDA2OTMyOC
wxMDUyODMzNDA1LC02NDA3MzgwNzUsLTM1OTM1NTY5OCw0MzUx
ODU0MCwtODM0ODQyODI3LDk3NzkzNDY1MiwxMjkzMTY1NzUxLD
E2NTMyMTgyNzksMzc3NzQ5MTUxLDE4Nzc4MTIwODAsMTQyMDQ0
ODYxXX0=
-->