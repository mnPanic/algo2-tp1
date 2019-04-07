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
esValida? : hab x pos -> bool
estaOcupada? : hab h x pos c -> bool           {esValida?(c, h)}
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

esConexa? : hab -> bool
posiciones : hab -> conj(pos)
posicionesLibres : hab -> conj(pos)

// auxiliares
filtrarLibres : hab x conj(pos) -> conj(pos)
verificarAlcance : hab x conj(pos) x conj(pos) -> bool
verificarAlcancePos : hab x pos x conj(pos) -> bool

// Funcion dada
es_alcanzable : hab x pos x pos -> bool
```

axiomatización
--------------

```text
esValida?(p, nueva(n)) ==  0 ≤ π_1(p) < n ^
                            0 ≤ π_2(p) < n

esValida?(p, ocupar(p', h)) == p = p' vL esValida?(p, h)

estaOcupada?(p, nuevo(n)) == false
estaOcupada?(p, ocupar(p', h)) == p = p'

tamaño(nueva(n)) == n
tamaño(ocupar(p, h)) == tamaño(h)

posicionesLibres(h) == filtrarLibres(posiciones(h))
filtrarLibres(ps) == if ø?(ps)
                      then ø
                      else (if estaOcupada?(dameUno(ps))
                            then ø
                            else {dameUno(ps)}
                            fi) U filtrarLibres(sinUno(ps))
                      fi

es_conexa(h) == verificarAlcance(h, posicionesLibres(h))

verificarAlcance(h, ps) == if ø?(ps)
                            then true
                            else (verificarAlcancePos(h, ps, dameUno(ps)) ^
                                  verificarAlcance(h, sinUno(ps)))
                            fi

verificarAlcancePos(h, ps, p) == if ø?(ps)
                                   then true
                                   else (es_alcanzable(h, p, dameUno(ps)) ^
                                         verificarAlcancePos(h, p, sinUno(ps)))
                                   fi

// TODO: axiomatizar posiciones @Schuster
```