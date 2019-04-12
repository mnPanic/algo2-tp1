// Le agregamos a la funcion %(modulo)

\*%* : nat x nat -> nat

n%m == if n < m
       then n
       else if n>=m
       then (n-m)%m
       else n+m
       fi
