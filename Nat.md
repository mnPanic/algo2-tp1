// Le agregamos a la funcion %(modulo)

\*%* : nat x nat -> nat

n%m == if n < m
       then n
       else (n-m)%m
       fi
