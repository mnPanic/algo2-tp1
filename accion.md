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
(mover(d)      =obs mover(d)) ^
(disparar      =obs disparar) ^
(nada          =obs nada)
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
mover      : direccion -> accion
mirar      : direccion -> accion
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

¬(mover(d))     = mover(opuesta(d))
¬(mirar(d))     = mirar(opuesta(d))
¬(disparar)     = disparar
¬(nada)         = nada

ubicacionLuegoDe(nada, h, u) == u
ubicacionLuegoDe(disparar, h, u) == u
ubicacionLuegoDe(mirar(d), h, u) == <pos(u), d>

ubicacionLuegoDe(mover(arriba), h, < <x, y>, dir >) ==
    <(if esValida?(h, < x, y + 1 >) ^L ¬ estaOcupada?(h, < x, y + 1 >)
    then < x, y + 1 >
    else < x, y >
    fi), arriba>

ubicacionLuegoDe(mover(abajo), h, < <x, y>, dir >) ==
    <(if esValida?(h, < x, y - 1 >) ^L ¬ estaOcupada?(h, < x, y - 1 >)
    then < x, y - 1 >
    else < x, y >
    fi), abajo>

ubicacionLuegoDe(mover(derecha), h, < <x, y>, dir >) ==
    <(if esValida?(h, < x + 1, y >) ^L ¬ estaOcupada?(h, < x + 1, y >)
    then < x + 1, y >
    else < x, y >
    fi), derecha>

ubicacionLuegoDe(mover(izquierda), h, < <x, y>, dir >) ==
    <(if esValida?(h, < x - 1, y >) ^L ¬ estaOcupada?(h, < x - 1, y >)
    then < x - 1, y >
    else < x, y >
    fi), izquierda>


// TODO: Axiomatizar posicionesAfectadas @Gasti
```