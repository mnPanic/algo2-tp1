TAD PJ ES NAT
=============

TAD Fantasma ES NAT
===================

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
(V j, j': juego) j =obs j' <=>
    accionesPJs(j) =obs accionesPJs(j') ^
    accionesFan(j) =obs accionesFan(j') ^
    hab(j) =obs hab(j') ^
    localizarJugadores(j) = localizarJugadores(j') ^
    (V p : pj) vivePJ?(j, p) =obs vivePJ?(j', p) ^
    (V f : fantasma) viveFan?(j, f) =obs viveFan?(j', f) ^
                     ubicacionInicialFan(j, f) =obs ubicacionInicialFan(j', f)

```

generadores
-----------

```text
iniciar : conj(pj) pjs x secu(acciones) as x ubicacion u x hab h -> juego
    {esConexa(h) ^ ¬ ø?(as) ^ ¬ ø?(pjs) ^ esValida?(h, pos(u))}

proxPaso : juego j x pj p x accion a -> juego
    {p € jugadores(j) ^L vivePJ?(j, p) ^ ¬ termino?(j) ^ ¬ esMirar(a)}
```

otras operaciones
-----------------

```text
ronda : juego -> nat
paso  : juego -> nat
cantAcciones : juego x conj(pj) -> nat


jugadores : juego -> conj(pj)
fantasmas : juego -> conj(fantasma)

terminaRonda : juego j x pj p x accion -> bool
    {p € jugadores(j)}
// dice si una ronda termino, i.e si murio el fantasma especial (el ultimo fantasma)

termino? : juego -> bool // dice si terminó un juego, i.e si murieron todos los jugadores

estanVivos : juego j x conj(pj) pjs -> bool         {pjs C jugadores(j)}

fantasmaEspecial : juego -> fantasma

ubicacionInicialPJ : juego j x pj p -> ubicacion     {p € jugadores(j)}
ubicacionPJ  : juego j x pj p -> ubicacion           {p € jugadores(j)}
ubicacionFan : juego j x fantasma f -> ubicacion     {f € fantasmas(j)}

deducirUbicacion : juego j x ubicacion u x acciones -> ubicacion
    {esValida?(hab(j), pos(u))}

moriraFantasma : juego j x pj p x accion x fantasma f -> bool
    {p € jugadores(j) ^ f € fantasmas(j)}

moriraPJ : juego j x conj(fantasma) fs x pj p x accion -> bool
    {p € jugadores(j) ^ fs C fantasmas(j)}

moriraPJPorFantasma : juego j x fantasma f x pj p x accion -> bool
    {p € jugadores(j) ^ f € fantasmas(j)}

accionFan : juego j x fantasma f -> accion  {f € fantasmas(j) ^L vivoFan?(j, f)}

puntaje : juego -> nat

inicializarAcciones : conj(pj) -> dicc(pj, secu(accion))

agregarFantasma : juego j x ubicacion u x dicc(fantasma, secu(accion)) x fantasma x secu(accion) -> dicc(fantasma, secu(accion))
    {esValida?(hab(j), pos(u))}

generarAccionesFantasma : juego j x ubicacion u x secu(accion) -> secu(accion) {esValida?(hab(j), pos(u))}

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
    // Si con la acción de p termina la ronda, reviven todos.
    terminaRonda?(j, p, a) v
    if (p = p')
    then ¬ moriraPJ(j, fantasmas(j), p, a)
    else vivePJ?(j, p') ^
         ¬ moriraPJ(j, fantasmas(j), p', nada)
    fi
    // Nota: Si p = p', entonces por la reestricción de proxPaso
    //       sabemos que está vivo en rondas anteriores, no hace falta preguntarlo


viveFan?(iniciar(pjs, as, u, h), f) == true
viveFan?(proxPaso(j, p, a), f) ==
    terminaRonda?(j, p, a) v
    (viveFan?(j, f) ^       // No tiene que haber muerto en pasos anteriores
    ¬ moriraFantasma(j, p, a, f))

ubicacionInicialFan(iniciar(pjs, as, u, h), f') == u

ubicacionInicialFan(proxPaso(j, p, a), f) ==
    // Si f no pertence a los fantasmas del juego antes de efectuar el paso, entonces es nuevo, y fue la acción de p la que finalizó la ronda, así agregando un nuevo fantasma.
    if f € fantasmas(j)
    then ubicacionInicialFan(j, f)
    else ubicacionInicialPJ(j, p)  // es nuevo
    fi


accionesPJs(iniciar(pjs, as, u, h)) ==
    inicializarAcciones(pjs)

accionesPJs(proxPaso(j, p, a)) ==
    if ¬ terminaRonda(j, p, a)
    then definir(p, obtener(p, accionesPJs(j)) ° a, accionesPJs(j))
    else inicializarAcciones(jugadores(j))
    fi


accionesFan(iniciar(pjs, as, u, h)) ==
    definir(nombreSiguienteFan(j), as, vacío)

accionesFan(proxPaso(j, p, a)) ==
    if ¬ terminaRonda(j, p, a)
    then accionesFan(j)
    // Como termina la ronda luego de la acción de p, p mató al fantasma especial.
    // Le agrego las acciones de p al nuevo fantasma
    else agregarFantasma(j,
                         ubicacionInicialPJ(j, p),
                         accionesFan(j),
                         nombreSiguienteFan(j),
                         obtener(p, accionesPJs(j)) ° a)

/////////////// otras operaciones
ronda(j) = #(fantasmas(j))
paso(j) = cantAcciones(j, jugadores(j))

cantAcciones(j, pjs) ==
    if ø?(pjs)
    then 0
    else long(obtener(dameUno(pjs)), accionesPJs(j)) + cantAcciones(j, sinUno(pjs))
    fi

jugadores(j) == claves(accionesPJs(j))
fantasmas(j) == claves(accionesFan(j))

termino?(j) == ¬ estanVivos(j, jugadores(j))

estanVivos(j, pjs) ==
    if ø?(pjs)
    then true
    else vivePJ?(dameUno(pjs)) ^ estanVivos(j, sinUno(pjs))


// Funciona por como está definida nombreSiguienteFan
fantasmaEspecial(j) == #(claves(accionesFan(j)))


ubicacionInicialPJ(j, p) == obtener(p, localizarJugadores(j))

ubicacionPJ(j, p) ==
    deducirUbicacion(j,
                     ubicacionInicialPJ(j, p)
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

agregarFantasma(j, uInicialPJ, accionesFantasmas, f, as) ==
    definir(f, generarAccionesFantasma(j, uInicialPJ, as), accionesFantasmas)

generarAccionesFantasma(j, uInicialPJ, as) ==
    as & (nada * nada * nada * nada * nada * <>) & invertir(hab(j), uInicialPJ, as)

inicializarAcciones(pjs) ==
    if ø?(pjs)
    then vacío
    else definir(dameUno(pjs), <>, inicializarAcciones(sinUno(pjs)))
    fi

terminaRonda(j, p, a) == moriraFantasma(j, p, a, fantasmaEspecial(j))

moriraFantasma(j, p, a, f) ==
    pos(ubicacionFan(j, f)) € posicionesAfectadasPor(a, hab(j), ubicacionPJ(j, p))

moriraPJ(j, fs, p, a) ==
    if ø?(fs)
    then false
    else (viveFan?(j, dameUno(fs)) ^L moriraPJPorFantasma(j, dameUno(fs), p, a) v
          moriraPJ(j, sinUno(fs), p, a))
    fi

moriraPJPorFantasma(j, f, p, a) ==
    ¬ moriraFantasma(j, p, a, f) ^
    pos(ubicacionLuegoDe(a, hab(j), ubicacionPJ(j, p))) € posicionesAfectadasPor(accionFan(j, f), hab(j), ubicacionFan(j, f))


accionFan(j, f) == (obtener(accionesFan(j), f))[paso(j) % obtener(accionesFan(j), f)]

puntaje(j) == ronda(j) - 1

nombreSiguienteFan(j) == #claves(accionesFan(j)) + 1
```
