TAD Direccion
=============

genero
------

```text
direccion
```

igualdad observacional
----------------------

```text
(arriba     =obs arriba) ^
(abajo      =obs abajo) ^
(izquierda  =obs izquierda) ^
(derecha    =obs derecha)
```

generadores
-----------

```text
arriba     : -> direccion
abajo      : -> direccion
izquierda  : -> direccion
derecha    : -> direccion
```

otras operaciones
-----------------

```text
opuesta : direccion -> direccion
```

axiomatización
--------------

```text
opuesta(arriba)     = abajo
opuesta(abajo)      = arriba
opuesta(izquierda)  = derecha
opuesta(derecha)    = izquierda
```