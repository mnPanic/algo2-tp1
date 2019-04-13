// Le agregamos a secuencia la otra operación de obtención por indexación

\*[\*] : secu(a) s x nat n -> a     {n < long(s)}

// TODO axiomatizar
s[i] == if i = 0
        then prim(s)
        else fin(s)[i - 1]
        fi
