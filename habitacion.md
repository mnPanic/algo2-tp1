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
ocupar : hab h x pos c -> hab               {esValida?(c, h) ^L ¬ estaOcupada?(c)}
```

otras operaciones
-----------------

```text
tamaño : hab -> nat

esConexa? : hab -> bool
posicionesLibres : hab -> conj(pos)

// auxiliares
filtrarLibres : hab x conj(pos) -> conj(pos)
verificarAlcance : hab x conj(pos) x conj(pos) -> bool
verificarAlcancePos : hab x pos x conj(pos) -> bool
darPosiciones : hab x nat x nat x nat - > conj(pos)
posiciones : hab -> conj(pos)

// Funcion dada
esAlcanzable : hab x pos x pos -> bool
```

axiomatización
--------------

```text
esValida?(p, nueva(n)) ==  0 ≤ π_1(p) < n ^
                            0 ≤ π_2(p) < n

esValida?(p, ocupar(p', h)) == p = p' vL esValida?(p, h)

estaOcupada?(p, nuevo(n)) == false
estaOcupada?(p, ocupar(p', h)) ==
    p = p' v estaOcupada?(p, h)

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

esConexa?(h) == verificarAlcance(h, posicionesLibres(h))

verificarAlcance(h, ps) == if ø?(ps)
                            then true
                            else (verificarAlcancePos(h, ps, dameUno(ps)) ^
                                  verificarAlcance(h, sinUno(ps)))
                            fi

verificarAlcancePos(h, ps, p) == if ø?(ps)
                                   then true
                                   else (esAlcanzable(h, p, dameUno(ps)) ^
                                         verificarAlcancePos(h, p, sinUno(ps)))
                                   fi

posiciones(h) == darPosiciones(h, tamaño(h)-1, tamaño(h)-1, tamaño(h)-1)

darPosiciones(h, n, m, tam) == if n = 0 ^ k = 0
                               then ø
                               else if k = 0
                               then Ag((n,k), darPosiciones(h, n-1, tam, tam))
                               else Ag((n,k), darPosiciones(h, n, k-1, tam))

// TODO: axiomatizar posiciones @Schuster
```
