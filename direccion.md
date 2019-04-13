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

(V d, d' : direccion) d =obs d' <=>
    esArriba(d) =obs esArriba(d') ^
    esAbajo(d) =obs esAbajo(d') ^
    esIzquierda(d) =obs esIzquierda(d') ^
    esDerecha(d) =obs esDerecha(d')
```

observadores
------------

esArriba    : direccion -> bool
esAbajo     : direccion -> bool
esIzquierda : direccion -> bool
esDerecha   : direccion -> bool

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
proxPosEnDir : direccion x posicion -> posicion
```

axiomatización
--------------

```text
opuesta(arriba)     = abajo
opuesta(abajo)      = arriba
opuesta(izquierda)  = derecha
opuesta(derecha)    = izquierda

proxPosEnDir(arriba, p) ==
    <π_1(p)
     π_2(p) + 1>

proxPosEnDir(abajo, p) ==
    <π_1(p)
     π_2(p) - 1>

proxPosEnDir(izquierda, p) ==
    <π_1(p) - 1
     π_2(p)>

proxPosEnDir(derecha, p) ==
    <π_1(p) + 1
     π_2(p)>

// Diviertanse
esArriba(arriba) = true
esArriba(abajo) = false
esArriba(izquierda) = false
esArriba(derecha) = false

esAbajo(arriba) = false
esAbajo(abajo) = true
esAbajo(izquierda) = false
esAbajo(derecha) = false

esIzquierda(arriba) = false
esIzquierda(abajo) = false
esIzquierda(izquierda) = true
esIzquierda(derecha) = false

esDerecha(arriba) = false
esDerecha(abajo) = false
esDerecha(izquierda) = false
esDerecha(derecha) = true
```