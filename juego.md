TAD PJ ES NAT
=============

TAD Fantasma ES NAT
===================

TAD Dirección ES String
=======================

TAD Ubicación ES tupla(posicion, direccion)
===========================================

TAD Juego
=========

generos
-------

```text
juego
```

observadores
------------

```text
accionesPJs : juego -> dicc(pj, secu(accion))
accionesFan : juego -> dicc(fantasma, secu(accion))

hab   : juego -> hab    // done
ronda : juego -> nat    // done
paso  : juego -> nat    // done

vivePJ?  : juego j x pj p -> bool               {p € jugadores(j)}  // done
viveFan? : juego j x fantasma f -> bool         {f € fantasmas(j)}  // done

ubicacionInicialFan : juego j x fantasma f -> ubicacion    {f € fantasmas(j)}   // done

// Dada
localizarJugadores : juego -> dicc(pj, ubicacion)
```

igualdad observacional
----------------------

```text
TODO
```

generadores
-----------

```text
iniciar : conj(pj) pjs x fantasma x secu(acciones) as x ubicacion x hab h -> juego
    {esConexa(h) ^ not ø?(as) ^ not ø?(pjs)}

proxPaso : juego j x pj p x accion a -> juego
    {p € jugadores(j) ^L vivePJ?(j, p) ^ not termino?(j)}
```

otras operaciones
-----------------

```text
jugadores : juego -> conj(pj)        // devuelve las claves de accionesPJs
fantasmas : juego -> conj(fantasma)  // devuelve las claves de accionesFan

terminoRonda : juego j x pj p x accion -> bool
    {p € jugadores(j)}
// dice si una ronda termino, i.e si murio el fantasma especial (el ultimo fantasma)

terminoJuego : juego -> bool // dice si terminó un juego, i.e si murieron todos los jugadores

fantasmaEspecial : juego -> fantasma

ubicacionPJ  : juego j x pj p -> ubicacion           {p € jugadores(j)}
ubicacionFan : juego j x fantasma f -> ubicacion     {f € fantasmas(j)}

pos : ubicacion -> posicion
dir : ubicacion -> dirección

moriraFantasma : juego j x pj p x accion x fantasma f -> bool
    {p € jugadores(j) ^ f € fantasmas(j)}

moriraPJ : juego j x conj(fantasma) fs x personaje p x accion -> bool
    {p € jugadores(j) ^ fs C fantasmas(j)}

moriraPorFantasma : juego j x fantasma f x pj p x accion -> bool
    {p € jugadores(j) ^ f € fantasmas(j)}

accionFan : juego j x fantasma f -> accion  {f € fantasmas(j) ^L vivoFan?(j, f)}

puntaje : juego -> nat

inicializarAcciones : conj(pj) -> dicc(pj, secu(accion))

agregarAccion : dicc(pj, secu(accion)) acciones x conj(pj) pjs x pj p x accion
    {pjs C claves(acciones) ^ p € pjs}
```

axiomatización
--------------

```text

// obs

hab(iniciar(pjs, f, as, u, h)) == h
hab(proxPaso(j, p, a)) == hab(j)

ronda(iniciar(pjs, f, as, u, h)) == 1
ronda(proxPaso(j, p, a)) ==
    ronda + ß(terminoRonda(j, p, a))

paso(iniciar(pjs, f, as, u, h)) == 1
paso(proxPaso(j, p, a)) ==
    if (terminoRonda(j, p, a))
    then 1
    else paso(j) + 1
    fi

vivePJ?(iniciar(pjs, f, as, u, h), p) == true
vivePJ?(proxPaso(j, p, a), p') ==
    if (p = p')
    then not moriraPJ(j, fantasmas(j), p, a)
    else vivePJ?(j, p')

viveFan?(iniciar(pjs, f, as, u, h), f) == true
viveFan?(proxPaso(j, p, a), f) ==
    not moriraFantasma(j, p, a, f)


// f = f' por la reestricción
ubicacionInicialFan(iniciar(pjs, f, as, u, h), f') == u
ubicacionInicialFan(proxPaso(j, p, a), f) ==
    // Si f no pertence a los fantasmas del juego antes de efectuar el paso, entonces es nuevo, y fue la acción de p la que finalizó la ronda, así agregando un nuevo fantasma.
    if f € fantasmas(j)
    then ubicacionInicialFan(j, f)
    else obtener(p, localizarJugadores(j))  // es nuevo
    fi

accionesPJs(iniciar(pjs, f, as, u, h)) ==
    inicializarAcciones(pjs)

accionesPJs(proxPaso(j, p, a)) ==
    agregarAccion(accionesPJs(j), personajes(j), p, a)

// otras operaciones

// Setea la acción a al personaje p, y al resto le pone nada, porque se mueve 1 solo
agregarAccion(acciones, pjs, p, a) ==
    if ø?(pjs)
    then vacio
    else if dameUno(pjs) == p
         then definir(p,
                      obtener(p, acciones) ° a,
                      agregarAccion(acciones, sinUno(pjs), p, a))

         else if vivePJ?(dameUno(pjs)
              then definir(dameUno(pjs),
                           obtener(dameUno(pjs), acciones) ° nada,
                           agregarAccion(acciones, sinUno(pjs), p, a))
              else agregarAccion(acciones, sinUno(pjs), p, a)
         fi
    fi


inicialiarAcciones(pjs) ==
    if ø?(pjs)
    then vacío
    else definir(dameUno(pjs), <>, inicializarAcciones(sinUno(pjs)))
    fi

terminoRonda(j, p, a) == moriraFantasma(j, p, a, fantasmaEspecial(j))

moriraFantasma(j, p, a, f) ==
    a = disparar ^
    pos(ubicacionFan(j, f)) € posicionesAfectadasPor(disparar, hab(j), ubicacionPJ(j, p))

moriraPJ(j, fs, p, a) ==
    if ø?(fs)
    then false
    else (moriraPorFantasma(j, dameUno(fs), p, a) v
          moriraPj(j, sinUno(fs), p, a))
    fi

moriraPorFantasma(j, f, p, a) ==
    not moriraFantasma(j, p, a, f) ^
    accionFan(j, f) = disparar ^
    pos(ubicacionLuegoDe(a, hab(j), ubicacionPJ(j, p))) € posicionesAfectadasPor(disparar, hab(j), ubicacionFan(j, f))


accionFan(j, f) == (obtener(accionesFan(j), f))[paso(j) % obtener(accionesFan(j), f)]

puntaje(j) == ronda(j) - 1
```