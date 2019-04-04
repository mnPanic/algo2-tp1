TAD Celda ES tupla(nat, nat)
============================

TAD Habitación
==============

observadores
------------

```
es_valida? : habitacion x celda -> bool
esta_ocupada? : habitacion h x celda c -> bool           {esValida?(c, h)}
```

igualdad observacional
----------------------

```
(V h1, h2 : habitacion) (h1 =obs h2) <=>
    (V c : celda) esValida(c, h1) =obs esValida(c, h2) ^L
                  esValida(c, h1) =>L (estaOcupada? (c, h1) =obs estaOcupada?(c, h2))
```

generadores
-----------

```
nueva : nat n -> habitacion                              {n > 1}
ocupar_celda : habitacion h x celda c -> habitacion      {esValida?(c, h)}   // TODO: Donde poner la restricción
```

otras operaciones
-----------------

```
tamaño : habitacion -> nat  (devuelve una sola dimensión)

// Funcion dada
es_alcanzable : habitacion x celda x celda -> bool

es_conexa? : habitacion -> bool

```

axiomatización
--------------

esta_ocupada?(nueva(n), c) = false
esta_ocupada?(ocupar(h, c'), c) =
    if c = c'
    then es_conexo(ocupar(h,c'))
    else esta_ocupada(h, c)?
    fi

es_conexa?(h) = c, c' : celda es_alcan    // TODO


// TODO: Usar dict?