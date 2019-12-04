# TL;DR
En este repositorio se encuentra el código de mi parte individual para la práctica 3 de base de datos "Gestión de usuarios". Soy el alumno 1, y me corresponde hacer la parte de Oracle.
La documentación de cada ejercicio con pruebas de funcionamiento, las podrás encontrar en este mismo README

---

## Ejercicio 1
Crea un rol ROLPRACTICA1 con los privilegios: 

- Conectarse a la base de datos
- Crear tablas y vistas
- Insertar datos en la tabla EMP de SCOTT.

---




```
CREATE ROLE ROLPRACTICA1;
Grant Create session to ROLPRACTICA1;
Grant Create table to ROLPRACTICA1;
Grant Create view to ROLPRACTICA1;
grant insert on scott.emp to ROLPRACTICA1;
```




## Ejercicio 2
1.  Crea un usuario USRPRACTICA1 con el tablespace USERS por defecto y averigua que cuota se le ha asignado por defecto en el mismo. Sustitúyela por una cuota de 1M.



## Ejercicio 3
1.  Modifica el usuario USRPRACTICA1 para que tenga cuota 0 en el tablespace SYSTEM.




## Ejercicio 4
1.  Concede a USRPRACTICA1 el ROLPRACTICA1.


## Ejercicio 5
1.  Concede a USRPRACTICA1 el privilegio de crear tablas e insertar datos en el esquema de cualquier usuario. Prueba el privilegio. Comprueba si puede modificar la estructura o eliminar las tablas creadas.


## Ejercicio 6
1.  Concede a USRPRACTICA1 el privilegio de leer la tabla DEPT de SCOTT con la posibilidad de que lo pase a su vez a terceros usuarios.


## Ejercicio 7
1.  Comprueba que USRPRACTICA1 puede realizar todas las operaciones previstas en el rol.


## Ejercicio 8
1.  Quita a USRPRACTICA1 el privilegio de crear vistas. Comprueba que ya no puede hacerlo.



## Ejercicio 9
1.  Crea un perfil NOPARESDECURRAR que limita a dos el número de minutos de inactividad permitidos en una sesión.


## Ejercicio 10
1.  Activa el uso de perfiles en ORACLE.



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




## Ejercicio 17



## Ejercicio 18






## Ejercicio 19




## Ejercicio 20




## Ejercicio 21




## Ejercicio 22




## Ejercicio 23





## Ejercicio 24




## Ejercicio 25









<!--stackedit_data:
eyJoaXN0b3J5IjpbMjAxNTk5NDkwNV19
-->