# Rutas de evacuación en una zona inundada
 
Prototipo que simula una zona inundada y busca la ruta más conveniente para que un agente llegue desde una zona afectada hasta un refugio, considerando qué zonas se pueden cruzar, cuáles están bloqueadas y qué tan riesgosa es cada una.
 
El objetivo no es solo encontrar *una* ruta, sino encontrar la ruta *más conveniente* y demostrar, con ejecuciones reales y pruebas, que las decisiones del algoritmo tienen sentido.
 
## Contenido del repositorio
 
| Archivo | Descripción |
|---|---|
| `rutas_inundacion.ipynb` | Notebook principal con todo el código, visualizaciones, pruebas, comparación y conclusiones. |
| `README.md` | Este archivo. |
 
## Cómo ejecutarlo
 
**En Google Colab (recomendado)**
1. Abrir [Google Colab](https://colab.research.google.com/).
2. Ir a *Archivo → Subir cuaderno* y elegir `rutas_inundacion.ipynb` (o abrirlo desde la pestaña *GitHub* con el enlace de este repositorio).
3. Ejecutar *Entorno de ejecución → Ejecutar todas*.
**En una computadora local**
```bash
pip install numpy pandas matplotlib jupyter
jupyter notebook rutas_inundacion.ipynb
```
 
Todas las tablas, gráficas y resultados se generan al ejecutar el notebook. Los mapas aleatorios usan semillas fijas, por lo que los resultados son reproducibles.
 
## Representación del terreno
 
El terreno es una cuadrícula donde cada celda tiene un nivel de inundación:
 
| Valor | Significado | ¿Se puede cruzar? | Riesgo |
|---|---|---|---|
| 0 | Seco | Sí | 0 |
| 1 | Inundación baja | Sí | 1 |
| 2 | Inundación media | Sí | 2 |
| 3 | Inundación alta | No (bloqueado) | — |
 
El agente se mueve en 4 direcciones (arriba, abajo, izquierda y derecha).
 
## Cómo se incorpora el riesgo
 
Cada vez que el agente entra a una celda paga un costo:
 
```
costo = 1 + α · riesgo
```
 
- El `1` representa la distancia recorrida.
- `α` indica cuánto pesa el riesgo frente a la distancia. Con `α = 0` solo importa la distancia; con valores mayores se prefiere caminar más para evitar el agua.
La mejor ruta es la de **menor costo total**. Así, si dos rutas llegan al refugio y una es más corta pero cruza más riesgo, el sistema compara sus costos totales y elige la menor. El notebook lo demuestra cambiando el valor de α sobre el mismo mapa.
 
## Estrategias de búsqueda
 
| Algoritmo | ¿Considera el riesgo? | ¿Garantiza la ruta de menor costo? |
|---|---|---|
| BFS | No | No (solo la de menos pasos) |
| DFS | No | No |
| Dijkstra (UCS) | Sí | Sí |
| A* (heurística Manhattan) | Sí | Sí |
| Greedy | No | No |
 
Para cada algoritmo se mide el costo de la ruta, el número de pasos, el riesgo acumulado, los **nodos expandidos** (trabajo realizado) y el tiempo de ejecución.
 
## Pruebas incluidas
 
Todas se ejecutan en mapas de 12x12 o mayores y muestran el resultado de forma visual:
 
1. Existe una ruta disponible hasta el refugio.
2. Se bloquea una zona que formaba parte de la ruta.
3. El refugio deja de ser accesible.
4. Se modifica el riesgo de una región y se vuelve a buscar.
5. El camino más corto deja de ser la alternativa más conveniente.
6. **Prueba adicional:** se compara A* contra Dijkstra en 300 mapas aleatorios para detectar errores en la heurística o en la cola de prioridad. Como control, se repite con una heurística defectuosa a propósito para comprobar que la prueba sí detecta el error. También se prueba que el validador rechace rutas inválidas.
Cada ruta se revisa con un validador independiente que comprueba que empiece en el inicio, termine en el refugio, no cruce celdas bloqueadas, no dé saltos y que su costo coincida con el recalculado desde el mapa.
 
## Uso de inteligencia artificial
 
En el desarrollo se utilizó IA como apoyo. El detalle de las herramientas utilizadas y de cómo se verificó el código está en la última sección del notebook.
 
