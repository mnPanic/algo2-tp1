TAD Pos ES tupla(nat, nat)
==========================

TAD Habitación
==============

generos
-------

```text
hab
```

observadores
------------

```text
es_valida? : hab x pos -> bool
esta_ocupada? : hab h x pos c -> bool           {esValida?(c, h)}
```

igualdad observacional
----------------------

```text
(V h1, h2 : hab) (h1 =obs h2) <=>
    (V c : pos) esValida(c, h1) =obs esValida(c, h2) ^L
                  esValida(c, h1) =>L (estaOcupada? (c, h1) =obs estaOcupada?(c, h2))
```

generadores
-----------

```text
nueva : nat n -> hab                        {n > 1}
ocupar : hab h x pos c -> hab               {esValida?(c, h) ^L not esta_ocupada(c)}
```

otras operaciones
-----------------

```text
tamaño : hab -> nat

es_conexa? : hab -> bool
posiciones : hab -> conj(pos)
posiciones_libres : hab -> conj(pos)

// auxiliares
filtrar_libres : hab x conj(pos) -> conj(pos)
verificar_alcance : hab x conj(pos) x conj(pos) -> bool
verificar_alcance_pos : hab x pos x conj(pos) -> bool

// Funcion dada
es_alcanzable : hab x pos x pos -> bool
```

axiomatización
--------------

```text
es_valida?(p, nueva(n)) ==  0 ≤ π_1(p) < n ^
                            0 ≤ π_2(p) < n

es_valida?(p, ocupar(p', h)) == p = p' vL es_valida?(p, h)

esta_ocupada?(p, nuevo(n)) == false
esta_ocupada?(p, ocupar(p', h)) == p = p'

tamaño(nueva(n)) == n
tamaño(ocupar(p, h)) == tamaño(h)

posiciones_libres(h) == filtrar_libres(posiciones(h))
filtrar_libres(ps) == if ø?(ps)
                      then ø
                      else (if esta_ocupada?(dameUno(ps))
                            then ø
                            else {dameUno(ps)}
                            fi) U filtrar_libres(sinUno(ps))
                      fi

es_conexa(h) == verificar_alcance(h, posiciones_libres(h))

verificar_alcance(h, ps) == if ø?(ps)
                            then true
                            else (verificar_alcance_pos(h, ps, dameUno(ps)) ^
                                  verificar_alcance(h, sinUno(ps)))
                            fi

verificar_alcance_pos(h, ps, p) == if ø?(ps)
                                   then true
                                   else (es_alcanzable(h, p, dameUno(ps)) ^
                                         verificar_alcance_pos(h, p, sinUno(ps)))
                                   fi

// TODO: axiomatizar posiciones @Schuster
```