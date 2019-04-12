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

hab   : juego -> hab

vivePJ?  : juego j x pj p -> bool               {p € jugadores(j)}
viveFan? : juego j x fantasma f -> bool         {f € fantasmas(j)}

ubicacionInicialFan : juego j x fantasma f -> ubicacion    {f € fantasmas(j)}

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
iniciar : conj(pj) pjs x secu(acciones) as x ubicacion x hab h -> juego
    {esConexa(h) ^ ¬ ø?(as) ^ ¬ ø?(pjs)}

proxPaso : juego j x pj p x accion a -> juego
    {p € jugadores(j) ^L vivePJ?(j, p) ^ ¬ termino?(j)}
```

otras operaciones
-----------------

```text
ronda : juego -> nat
paso  : juego -> nat
cantPasos : juego x conj(pj) -> nat

pos : ubicacion -> posicion
dir : ubicacion -> dirección

jugadores : juego -> conj(pj)
fantasmas : juego -> conj(fantasma)

terminaRonda : juego j x pj p x accion -> bool
    {p € jugadores(j)}
// dice si una ronda termino, i.e si murio el fantasma especial (el ultimo fantasma)

termino? : juego -> bool // dice si terminó un juego, i.e si murieron todos los jugadores

estanVivos : juego j x conj(pj) pjs -> bool         {pjs C jugadores(j)}

fantasmaEspecial : juego -> fantasma

ubicacionPJ  : juego j x pj p -> ubicacion           {p € jugadores(j)}
ubicacionFan : juego j x fantasma f -> ubicacion     {f € fantasmas(j)}

deducirUbicacion : juego j x ubicacion u x acciones -> ubicacion
    {esValida?(hab(j), u)}

moriraFantasma : juego j x pj p x accion x fantasma f -> bool
    {p € jugadores(j) ^ f € fantasmas(j)}

moriraPJ : juego j x conj(fantasma) fs x pj p x accion -> bool
    {p € jugadores(j) ^ fs C fantasmas(j)}

moriraPorFantasma : juego j x fantasma f x pj p x accion -> bool
    {p € jugadores(j) ^ f € fantasmas(j)}

accionFan : juego j x fantasma f -> accion  {f € fantasmas(j) ^L vivoFan?(j, f)}

puntaje : juego -> nat

inicializarAcciones : conj(pj) -> dicc(pj, secu(accion))

agregarAccion : dicc(pj, secu(accion)) acciones x conj(pj) pjs x pj p x accion
    {pjs C claves(acciones) ^ p € pjs}

agregarFantasma : dicc(fantasma, secu(accion)) x fantasma x secu(accion) -> dicc(fantasma, secu(accion))

nombreSiguienteFan : juego -> fantasma
```

axiomatización
--------------

```text

/////////////// observadores
hab(iniciar(pjs, as, u, h)) == h
hab(proxPaso(j, p, a)) == hab(j)


vivePJ?(iniciar(pjs, as, u, h), p) == true
vivePJ?(proxPaso(j, p, a), p') ==
    if (p = p')
    then ¬ moriraPJ(j, fantasmas(j), p, a)
    else vivePJ?(j, p')


viveFan?(iniciar(pjs, as, u, h), f) == true
viveFan?(proxPaso(j, p, a), f) ==
    ¬ moriraFantasma(j, p, a, f)


ubicacionInicialFan(iniciar(pjs, as, u, h), f') == u

ubicacionInicialFan(proxPaso(j, p, a), f) ==
    // Si f no pertence a los fantasmas del juego antes de efectuar el paso, entonces es nuevo, y fue la acción de p la que finalizó la ronda, así agregando un nuevo fantasma.
    if f € fantasmas(j)
    then ubicacionInicialFan(j, f)
    else obtener(p, localizarJugadores(j))  // es nuevo
    fi


accionesPJs(iniciar(pjs, as, u, h)) ==
    inicializarAcciones(pjs)

accionesPJs(proxPaso(j, p, a)) ==
    if ¬ terminaRonda(j, p, a)
    then definir(p, obtener(p, acciones) ° a, accionesPJs(j))
    else inicializarAcciones(jugadores(j))
    fi


accionesFan(iniciar(pjs, as, u, h)) ==
    definir(nombreSiguienteFan(j), as, vacío)

accionesFan(proxPaso(j, p, a)) ==
    if ¬ terminaRonda(j, p, a)
    then accionesFan(j)
    // Como termina la ronda luego de la acción de p, p lo mató.
    // Le agrego las acciones de p al nuevo fantasma
    else agregarFantasma(accionesFan(j),
                         nombreSiguienteFan(j),
                         obtener(p, accionesPJs(j)) ° a)

/////////////// otras operaciones
ronda(j) = #(fantasmas(j))
paso(j) = cantPasos(j, jugadores(j))

cantPasos(j, pjs) == if ø?(pjs)
                     then 0
                     else long(obtener(dameUno), accionesPJs(j)) + cantPasos(j, sinUno(pjs))
                     fi


pos(u) = π_1(u)
dir(u) = π_2(u)


jugadores(j) == claves(accionesPJs(j))
fantasmas(j) == claves(accionesFan(j))

termino?(j) == estanVivos(j, jugadores(j))

estanVivos(j, pjs) ==
    if ø?(pjs)
    then true
    else vivePJ?(dameUno(pjs)) ^ estanVivos(j, sinUno(pjs))


// Funciona por como está definida nombreSiguienteFan
fantasmaEspecial(j) == #(claves(accionesFan(j)))


ubicacionPJ(j, p) ==
    deducirUbicacion(j,
                     obtener(p, localizarJugadores(j))
                     obtener(p, accionesPJs(j)))

ubicacionFan(j, f) ==
    deducirUbicacion(j,
                     ubicacionInicialFan(j, f)
                     obtener(f, accionesFan(j)))


deducirUbicacion(j, u, as) ==
    if vacía?(as)
    then u
    else deducirUbicacion(j,
                          ubicacionLuegoDe(prim(as), hab(j), u),
                          fin(as))
    fi

agregarFantasma(accionesFantasmas, f, as) ==
    definir(f, generarAccionesFantasma(as), accionesFantasmas)

generarAccionesFantasma(as) ==
    as & (nada * nada * nada * nada * nada * <>) & invertir(as)


inicializarAcciones(pjs) ==
    if ø?(pjs)
    then vacío
    else definir(dameUno(pjs), <>, inicializarAcciones(sinUno(pjs)))
    fi

terminaRonda(j, p, a) == moriraFantasma(j, p, a, fantasmaEspecial(j))

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
    ¬ moriraFantasma(j, p, a, f) ^
    accionFan(j, f) = disparar ^
    pos(ubicacionLuegoDe(a, hab(j), ubicacionPJ(j, p))) € posicionesAfectadasPor(disparar, hab(j), ubicacionFan(j, f))


accionFan(j, f) == (obtener(accionesFan(j), f))[paso(j) % obtener(accionesFan(j), f)]

puntaje(j) == ronda(j) - 1

nombreSiguienteFan(j) == #claves(accionesFan(j)) + 1
```
