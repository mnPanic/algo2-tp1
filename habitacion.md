TAD Pos ES tupla(nat, nat)
==========================

TAD Habitación
==============

generos
-------

```text
hab
```

exporta
-------
generos, observadores, generadores, esCoenxa?

observadores
------------

```text
esValida? : hab x pos -> bool
estaOcupada? : hab h x pos p -> bool           {esValida?(h, p)}
```

igualdad observacional
----------------------

```text
(V h1, h2 : hab) (h1 =obs h2) <=>
    (V p : pos) (esValida(p, h1) =obs esValida(p, h2)) ^L
                esValida(p, h1) =>L (estaOcupada? (p, h1) =obs estaOcupada?(p, h2))
```

generadores
-----------

```text
nueva  : nat n -> hab                        {n > 1}  // Decisión tomada, tiene que ser de 2 o mas porque sino nadie muere.
ocupar : hab h x pos p -> hab                {esValida?(h, p) ^L ¬ estaOcupada?(h, p)}
```

otras operaciones
-----------------

```text
esConexa? : hab -> bool

// auxiliares
tamaño : hab -> nat
posicionesLibres : hab h x conj(pos) ps -> conj(pos)             {ps C posiciones(h)}
verificarAlcance : hab h x conj(pos) ps -> bool       {ps C posiciones(h)}
verificarAlcancePos : hab h x conj(pos) ps x pos p -> bool   {ps C posiciones(h) ^ p € posiciones(h)}

posiciones : hab -> conj(pos)

darPosiciones : hab x nat x nat x nat - > conj(pos)


// Funcion dada
esAlcanzable : hab x pos x pos -> bool
```

axiomatización
--------------

```text
esValida?(nueva(n), p) ==  0 ≤ π_1(p) < n ^
                           0 ≤ π_2(p) < n

esValida?(ocupar(h, p'), p) == p = p' vL esValida?(h, p)

estaOcupada?(nuevo(n), p) == false
estaOcupada?(ocupar(h, p'), p) ==
    p = p' v estaOcupada?(h, p)

tamaño(nueva(n)) == n
tamaño(ocupar(h, p)) == tamaño(h)

esConexa?(h) == verificarAlcance(h, posicionesLibres(posiciones(h)))

posicionesLibres(h, ps) ==
      if ø?(ps)
      then ø
      else (if estaOcupada?(h, dameUno(ps))
            then ø
            else {dameUno(ps)}
            fi) U posicionesLibres(h, sinUno(ps))
      fi


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

darPosiciones(h, n, k, tam) == if n = 0 ^ k = 0
                               then ø
                               else if k = 0
                               then Ag((n,k), darPosiciones(h, n-1, tam, tam))
                               else Ag((n,k), darPosiciones(h, n, k-1, tam))
```
