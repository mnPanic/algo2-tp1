TAD Acción
==========

generos
-------

```text
acción
```

igualdad observacional
----------------------

```text
(arriba     =obs arriba) ^
(abajo      =obs abajo) ^
(izquierda  =obs izquierda) ^
(derecha    =obs derecha) ^
(disparar   =obs disparar) ^
(nada       =obs nada)
```

otras operaciones
-----------------

```text
ubicacionLuegoDe : accion x hab h x ubicacion u -> ubicacion    {esValida(h, pos(u))}

posicionesAfectadasPor : accion a x hab h x ubicacion u -> conj(pos) {esValida(h, pos(u))}
// No tiene en cuenta la posición desde la cual se dispara.

¬\* : accion -> accion

invertir : secu(accion) -> secu(accion)
```

generadores
-----------

```text
arriba     : -> accion
abajo      : -> accion
izquierda  : -> accion
derecha    : -> accion
disparar   : -> accion
nada       : -> accion
```

axiomatización
--------------

```text
invertir(as) ==
    if vacía?(as)
    then <>
    else ¬(ult(as)) * invertir(com(as))
    fi

¬(arriba)     = abajo
¬(abajo)      = arriba
¬(izquierda)  = derecha
¬(derecha)    = izquierda
¬(disparar)   = disparar
¬(nada)       = nada

ubicacionLuegoDe(nada, h, u) == u
ubicacionLuegoDe(disparar, h, u) == u

ubicacionLuegoDe(arriba, h, < <x, y>, dir >) ==
    <(if esValida?(h, < x, y + 1 >) ^L ¬ estaOcupada?(h, < x, y + 1 >)
    then < x, y + 1 >
    else < x, y >
    fi), "arriba">

ubicacionLuegoDe(abajo, h, < <x, y>, dir >) ==
    <(if esValida?(h, < x, y - 1 >) ^L ¬ estaOcupada?(h, < x, y - 1 >)
    then < x, y - 1 >
    else < x, y >
    fi), "abajo">

ubicacionLuegoDe(derecha, h, < <x, y>, dir >) ==
    <(if esValida?(h, < x + 1, y >) ^L ¬ estaOcupada?(h, < x + 1, y >)
    then < x + 1, y >
    else < x, y >
    fi), "derecha">

ubicacionLuegoDe(izquierda, h, < <x, y>, dir >) ==
    <(if esValida?(h, < x - 1, y >) ^L ¬ estaOcupada?(h, < x - 1, y >)
    then < x - 1, y >
    else < x, y >
    fi), "izquierda">

posicionesAfectadasPor : accion a x hab h x ubicacion u -> conj(pos) {esValida(h, pos(u))}

posicionesAfectadasPor(arriba, h, u) == ø
posicionesAfectadasPor(abajo, h, u) == ø
posicionesAfectadasPor(izquierda, h, u) == ø
posicionesAfectadasPor(derecha, h, u) == ø
posicionesAfectadasPor(nada, h, u) == ø

posicionesAfectadasPor(disparar, h, <x, y>, arriba >) == 
    if esValida?(h, < x, y + 1 >) ^L ¬estaOcupada?(h, < x, y + 1 >)
    then Ad(< x, y + 1 >, posicionesAfectadasPor(disparar, h, <x, y + 1>, arriba >))
    else ø
    fi
posicionesAfectadasPor(disparar, h, <x, y>, abajo >) == 
    if esValida?(h, < x, y - 1 >) ^L ¬estaOcupada?(h, < x, y - 1 >)
    then Ad(< x, y - 1 >, posicionesAfectadasPor(disparar, h, <x, y - 1>, abajo >))
    else ø
    fi
posicionesAfectadasPor(disparar, h, <x, y>, derecha >) == 
    if esValida?(h, < x + 1, y >) ^L ¬estaOcupada?(h, < x + 1, y>)
    then Ad(< x + 1, y>, posicionesAfectadasPor(disparar, h, <x + 1, y >, derecha >))
    else ø
    fi
posicionesAfectadasPor(disparar, h, <x, y>, izquierda >) ==  
    if esValida?(h, < x - 1, y >) ^L ¬estaOcupada?(h, < x - 1, y >)
    then Ad(< x - 1, y >, posicionesAfectadasPor(disparar, h, < x - 1, y >, izquierda >))
    else ø
    fi


// TODO: Axiomatizar posicionesAfectadas @Gasti
```