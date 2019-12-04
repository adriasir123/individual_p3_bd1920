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
Grant Create session to ROLPRACTICA1;
Grant Create table to ROLPRACTICA1;
Grant Create view to ROLPRACTICA1;
grant insert on scott.emp to ROLPRACTICA1;
```
### Prueba de funcionamiento



## Ejercicio 2
Crea un usuario USRPRACTICA1 con el tablespace USERS por defecto
Averigua qué cuota se le ha asignado por defecto en el mismo
Sustitúyela por una cuota de 1M

```
create user USRPRACTICA1 default tablespace users;  
  
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
Concede a USRPRACTICA1 los privilegios:
- Crear tablas en el esquema de cualquier usuario
- Insertar datos en el esquema de cualquier usuario
Probar los privilegios
Comprueba si puede modificar la estructura o eliminar las tablas creadas.

----

```

```



## Ejercicio 6
Concede a USRPRACTICA1 el privilegio de leer la tabla DEPT de SCOTT con la posibilidad de que lo pase a su vez a terceros usuarios.





## Ejercicio 7
Comprueba que USRPRACTICA1 puede realizar todas las operaciones previstas en el rol.




## Ejercicio 8
Quita a USRPRACTICA1 el privilegio de crear vistas. Comprueba que ya no puede hacerlo.






## Ejercicio 9
Crea un perfil NOPARESDECURRAR que limita a dos el número de minutos de inactividad permitidos en una sesión.





## Ejercicio 10
Activa el uso de perfiles en ORACLE.





## Ejercicio 11
1.  Asigna el perfil creado a USRPRACTICA1 y comprueba su correcto funcionamiento.





## Ejercicio 12
1.  Crea un perfil CONTRASEÑASEGURA especificando que la contraseña caduca mensualmente y sólo se permiten tres intentos fallidos para acceder a la cuenta. En caso de superarse, la cuenta debe quedar bloqueada indefinidamente.


## Ejercicio 13
1.  Asigna el perfil creado a USRPRACTICA1 y comprueba su funcionamiento. Desbloquea posteriormente al usuario.


## Ejercicio 14
1.  Consulta qué usuarios existen en tu base de datos.


## Ejercicio 15
1.  Elige un usuario concreto y consulta qué cuota tiene sobre cada uno de los tablespaces.


## Ejercicio 16
1.  Elige un usuario concreto y muestra qué privilegios de sistema tiene asignados.



## Ejercicio 17
1.  Elige un usuario concreto y muestra qué privilegios sobre objetos tiene asignados.


## Ejercicio 18
Consulta qué roles existen en tu base de datos.



## Ejercicio 19
1.  Elige un rol concreto y consulta qué usuarios lo tienen asignado.



## Ejercicio 20
1.  Elige un rol concreto y averigua si está compuesto por otros roles o no.



## Ejercicio 21
1.  Consulta qué perfiles existen en tu base de datos.



## Ejercicio 22
1.  Elige un perfil y consulta qué límites se establecen en el mismo.



## Ejercicio 23
1.  Muestra los nombres de los usuarios que tienen limitado el número de sesiones concurrentes.




## Ejercicio 24
1.  Realiza un procedimiento que reciba un nombre de usuario y un privilegio de sistema y nos muestre el mensaje 'SI, DIRECTO' si el usuario tiene ese privilegio concedido directamente, 'SI, POR ROL' si el usuario tiene ese privilegio en alguno de los roles que tiene concedidos y un 'NO' si el usuario no tiene dicho privilegio.



## Ejercicio 25
1.  Realiza un procedimiento llamado _MostrarNumSesiones_ que reciba un nombre de usuario y muestre el número de sesiones concurrentes que puede tener abiertas como máximo y las que tiene abiertas realmente.








<!--stackedit_data:
eyJoaXN0b3J5IjpbMzE3Mjk5NzIwLDE5NDY3MjgyMCwtMjQ4Nj
cwNjQyLDE1ODc5MDkzNjEsLTQxMjE2NjUyMV19
-->