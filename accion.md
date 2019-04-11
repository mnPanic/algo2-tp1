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
(mirar(d)      =obs mirar(d)) ^
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

invertir : hab x secu(accion) -> secu(accion)

esMirar : accion -> bool

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
invertir(h, u, as) ==
    if vacía?(as)
    then <>
    else ¬(ult(as), h, u) * invertir(h,
                                     com(as),
                                     ubicacionLuegoDe(ult(as), h, u))
    fi

¬(mover(d), h, u) ==
    // Si el jugador al moverse en esa dirección se hubiese quedado en el lugar
    // (i.e chocado contra la pared)
    // Entonces el fantasma no debería moverse para el otro lado, sino que debería simplemente mirar en la dirección opuesta.
    if ubicacionLuegoDe(mover(d), h, u) = u     // No se movió
    then mirar(opuesta(d))
    // Si se hubiese movido, entonces deberá moverse en la dirección opuesta
    else mover(opuesta(d))

¬(mirar(d), h, u) == mirar(opuesta(d))
¬(disparar, h, u) == disparar
¬(nada, h, u) == nada

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

esMirar(mirar(d))   == true
esMirar(moverse(d)) == false
esMirar(disparar)   == false
esMirar(nada)       == false


// TODO: Axiomatizar posicionesAfectadas @Gasti
```