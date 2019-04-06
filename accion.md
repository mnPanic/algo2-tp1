TAD Acción
==========

generos
-------

```text
acción
```

observadores
------------

```text
ubicacionLuegoDe : accion x hab h x ubicacion u -> ubicacion    {esValida(h, pos(u))}
```

igualdad observacional
----------------------

```text
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
ubicacionLuegoDe(nada, h, u) == u
ubicacionLuegoDe(disparar, h, u) == u

ubicacionLuegoDe(arriba, h, < <x, y>, dir >) ==
    <(if esValida?(h, < x, y + 1 >) ^L not estaOcupada?(h, < x, y + 1 >)
    then < x, y + 1 >
    else < x, y >
    fi), "arriba">

ubicacionLuegoDe(abajo, h, < <x, y>, dir >) ==
    <(if esValida?(h, < x, y - 1 >) ^L not estaOcupada?(h, < x, y - 1 >)
    then < x, y - 1 >
    else < x, y >
    fi), "abajo">

ubicacionLuegoDe(derecha, h, < <x, y>, dir >) ==
    <(if esValida?(h, < x + 1, y >) ^L not estaOcupada?(h, < x + 1, y >)
    then < x + 1, y >
    else < x, y >
    fi), "derecha">

ubicacionLuegoDe(izquierda, h, < <x, y>, dir >) ==
    <(if esValida?(h, < x - 1, y >) ^L not estaOcupada?(h, < x - 1, y >)
    then < x - 1, y >
    else < x, y >
    fi), "izquierda">
```