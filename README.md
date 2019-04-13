# algo2-tp1
Trabajo práctico de especificación de Algoritmos II

## Division de tareas
Pasado a latex:

- Manu: juego
- Schuster: habitacion, secuencia, ubicacion, NAT
- Gasti: accion, direccion


## Notas sobre enunciado

- Habitación: Grilla cuadrada con casilleros
  - Casilleros
    - Libres
      - Pueden ser ocupados por fantasmas o PJ.
      - Pueden contener varios PJs o fantasmas.
      - Todos los casilleros libres deben ser alcanzables desde otro libre (mapa conexo)
    - Ocupados
  - Los PJ y fantasmas siempre están en un casillero
  - Solaparse sobre un casillero no tiene efectos para ninguno

- Personaje (PJ)
  - Puede haber mas de uno
  - Tiene dirección en la que mira
  - Acciones
    - Nada
    - Moverse
      - 4 direcciones: Arriba, abajo, izquierda y derecha.
      - No se puede mover a un casillero ocupado.
      - Al moverse siempre queda mirando en la dirección en la que se mueve.
    - Disparar
      - Dispara en la dirección en la que mira.
      - Instantaneos
      - Afectan desde el casillero junto al personaje en la dirección en la que apunta
      - Siguen en linea recta hasta el primer casillero ocupado o el fin del mapa.
      - Cuando dispara cualquier fantasma en esa linea muere
- Fantasma
  - Cuando dispara cualquier PJ en esa linea muere

Si un fantasma y un personaje están en el mismo casillero y disparan, no pasa nada.

- Partida
  - Sucesión de rondas
  - El ultimo fantasma que se agrega es el _fantasma especial_
  - Cada ronda termina cuando un PJ elimina al _último_ fantasma agregado (*especial*).
  - No es necesario eliminar al resto para que la ronda termine, pero se puede.
  - Al eliminar al especial, *inmediatamente* comienza la siguiente ronda.
  - Inicio de ronda
    - Aparecen los fantasmas anteriores y uno nuevo que realiza las acciones del PJ que lo mató.
    - Todos los PJ, incluso los muertos, reaparecen.
  - Fantasma especial (que repite movimientos):
    - Los movimientos se realizan primero en dirección _adelante_ y luego _inversa_.
    - De forma que el fantasma va desde donde empezó el PJ hasta donde mató el fantasma principal y luego vuelve al lugar de inicio, esperando 5 steps de tiempo en el medio.
    - Dispara en los mismos momentos y direcciones que disparó el PJ en el camino de ida
    - Momentos y orden contrario y con el sentido invertido al volver.
  - Fin de partida
    - Mueren todos los PJ
    - Puntaje: cantidad de rondas sobrevividas `(ronda actual - 1?)`
  - Primer fantasma:
    - En la primera ronda no hay histroai de movimientos a copiar
    - Se define una secuencia de acciones para el primer fantasma
  - Tiempo del juego:
    - Serie de pasos discretos.
    - En un paso pueden o no suceder acciones de un fantasma o PJ.
    - Solo un PJ puede efectuar una acción en cada paso.
    - Orden de ejecución de acciones: Primero PJ después fantasma.
    - Cuando un fantasma termina de ejecutar las acciones en una dirección, *espera 5 pasos* sin hacer nada antes de realizarlos en la dir opuesta.
  - Posicion inicial de los fantasmas:
    - Dada por las trayectorias de los PJ que los mataron.
    - "Dada" para el primer fantasma
  - Posicion inicial de jugadores:
    - Abstraida a la función `localizar_jugadores`, Dado un Juego devuelve un dict de jugadores a direcciones y posiciones en el mapa.
