TAD Acción
==========

generos
-------

```text
acción
```

exporta
-------
observadores, generadores, genero, otras operaciones

igualdad observacional
----------------------

```text
(V a, a' : accion) a =obs a' <=>
    esNada(a) = esNada(a') y
    esDisparar(a) = esDisparar(a') y
    esMover(a) = esMover(a') y
    esMirar(a) = esMirar(a') y

    esMover(a) o esMirar(a) implica_luego
    direccion(a) =obs direccion(a')
```

observadores
------------

esMover    : accion -> bool
esMirar    : accion -> bool
esDisparar : accion -> bool
esNada     : accion -> bool

direccion : accion a -> direccion     {esMirar(a) v esMover(a)}

otras operaciones
-----------------

```text
ubicacionLuegoDe : accion x hab h x ubicacion u -> ubicacion    {esValida?(h, pos(u))}

posicionesAfectadasPor : accion a x hab h x ubicacion u -> conj(pos) {esValida?(h, pos(u))}
// No tiene en cuenta la posición desde la cual se dispara.

¬\* : accion -> accion

invertir : hab h x ubicacion u x secu(accion) -> secu(accion)       {esValida?(h, pos(u))}
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
posicionesAfectadasPor(mover(d), h, u) = ø
posicionesAfectadasPor(mirar(d), h, u) = ø
posicionesAfectadasPor(nada, h, u) = ø

posicionesAfectadasPor(disparar, h, u) =
    if esValida?(h, proxPosEnDir(dir(u), pos(u)) ^L ¬ estaOcupada?(h, proxPosEnDir(dir(u), pos(u)))
    then Ag(proxPosEnDir(dir(u), pos(u)),
            posicionesAfectadasPor(disparar,
                                   h,
                                   <proxPosEnDir(dir(u), pos(u)), dir(u)>))
    else ø

invertir(h, u, as) ==
    if vacía?(as)
    then <>
    else invertir(h, ubicacionLuegoDe(prim(as), h, u), fin(as)) o ¬(prim(as), h, u)
    fi

¬(mover(d), h, u) ==
    // Si el jugador al moverse en esa dirección se hubiese quedado en el lugar
    // (i.e chocado contra la pared)
    // Entonces el fantasma no debería mover para el otro lado, sino que debería simplemente mirar en la dirección opuesta.
    if pos(ubicacionLuegoDe(mover(d), h, u)) = pos(u)     // No se movió
    then mirar(opuesta(d))
    // Si se hubiese movido, entonces deberá mover en la dirección opuesta
    else mover(opuesta(d))

¬(mirar(d), h, u) == mirar(opuesta(d))
¬(disparar, h, u) == disparar
¬(nada, h, u) == nada

ubicacionLuegoDe(nada, h, u) == u
ubicacionLuegoDe(disparar, h, u) == u
ubicacionLuegoDe(mirar(d), h, u) == <pos(u), d>

ubicacionLuegoDe(mover(d), h, u) ==
    <(if esValida?(h, proxPosEnDir(d, pos(u))) ^L
         ¬ estaOcupada?(h, proxPosEnDir(d, pos(u)))
      then proxPosEnDir(d, pos(u))
      else pos(u)
      fi), d>

// Diviertanse
esMirar(mirar(d))   == true
esMirar(mover(d)) == false
esMirar(disparar)   == false
esMirar(nada)       == false

esMover(mirar(d)) = false
esMover(mover(d)) = true
esMover(disparar) = false
esMover(nada) = false

esDisparar(mirar(d)) = false
esDisparar(mover(d)) = false
esDisparar(disparar) = true
esDisparar(nada) = false

esNada(mirar(d)) = false
esNada(mover(d)) = false
esNada(disparar) = false
esNada(nada) = true

direccion(mirar(d)) = d
direccion(mover(d)) = d
```