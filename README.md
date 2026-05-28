# Actividad 9 Planeacion de Trayectorias

En este repositorio se genera la planeación de trayectorias de un robot móvil de accionamiento diferencial para que su recorrido dibuje la imagen de la actividad planteada. Para lograrlo, se extrajeron coordenadas específicas a partir de una imagen de referencia y se implementó un algoritmo de control para que el robot siguiera la ruta con la mayor presicion posible a los rasgos físicos (orejas, contorno del rostro, hocico, etc.).

---

## 2. Obtención de Puntos (Waypoints)
Para garantizar que la trayectoria capturara a la perfección la forma del rostro del felino, se desarrolló un script personalizado en MATLAB, esto debido a que GEOGEBRA tenía complicaciones para marcar todos los puntos de la imegen y extraerlos. 

* **Mapeo en vivo:** El script permite cargar la imagen original y dar clics manuales sobre los bordes y detalles de la imagen.
* **Ajuste del plano Cartesiano:** Las imágenes digitales tienen su origen (0,0) en la esquina superior izquierda, lo que causaba que la ruta saliera de cabeza en el simulador. Para solucionarlo, el algoritmo restó la coordenada Y capturada a la altura total de la imagen, mapeando perfectamente la imagen al plano cartesiano del entorno físico del robot.
* **Densidad de puntos:** Se tomó una alta densidad de puntos en zonas de curvas complejas (como los ojos o el contorno de las orejas) para asegurar que el algoritmo de interpolación tuviera suficiente información.

---

## 3. Comparación y Justificación de la Estrategia de Control

Para este reto se evaluaron dos aproximaciones de control cinemático, donde al final se decidió utilizar **Pure Pursuit** sobre el **Control de Posición Punto a Punto** . Se descartó el Control de Posición debido a que obliga al robot a detenerse y girar en cada coordenada, lo que genera movimientos robóticos o bruscos y aumenta drásticamente el tiempo de simulación por las paradas constantes. En su lugar, se seleccionó el algoritmo Pure Pursuit como la técnica más eficiente esto ya que al hacer los ejercicio previos denotó una mejora en el seguimineto de trayectorias, esto hace que el robot persiga un punto virtual a cierta distancia (Lookahead), donde se logra un movimiento continuo con una velocidad lineal optimizada. Esto permite trazar curvas suaves y simples, un comportamiento ideal y estrictamente necesario para dibujar formas naturales o complicadas. 

---

## 4. Ajuste de Parámetros Cinemáticos

Para que la imagen resultara perfecta y no hubiera recortes bruscos en las curvas de las orejas o la mandíbula, se sintonizaron los parámetros utilizando valores de la actividad anterior en el controlador `controllerPurePursuit`:

* **`LookaheadDistance = 0.4` m:** Es la distancia de visión del robot. Se eligió un valor bajo (0.4) para que el robot se apegara estrictamente a los detalles finos de los waypoints ya que si el valor fuera muy alto, el robot "recortaría" las esquinas o pasaría a otros puntos que no son los descritos en la secuencia.
* **`DesiredLinearVelocity = 4` m/s:** Una velocidad moderada-alta que permite que el robot complete el dibujo compuesto por cientos de puntos en un tiempo de simulación razonable, sin perder estabilidad.
* **`MaxAngularVelocity = 90` rad/s:** Una velocidad de giro bastante alta para permitirle al robot hacer giros cerrados casi sobre su propio eje, lo cual es vital para los vértices afilados del dibujo, también permite hacer lineas estrechas y seguir la trayectoria de manera perfecta 
* **`sampleTime = 0.1` s:** Un tiempo de muestreo lo suficientemente pequeño para que el control discreto reaccione rápido a los cambios de dirección continuos.

---


## 6. Resultados y Comparación

Al ejecutar la simulación en MATLAB, se obtuvo una representación visual muy fiel a la imagen. A continuación, se presenta la comparación entre la imagen original utilizada para la extracción de los *waypoints* y el resultado final del seguimiento de trayectoria realizado por el robot diferencial:

<p align="center">
  <img src="https://github.com/user-attachments/assets/04485c4a-cc1c-46f0-a2c7-1cb15821da79" width="48%" alt="Imagen Original del Felino" />
  <img src="https://github.com/user-attachments/assets/e8dc2d79-09d4-4d4f-8239-6394e0631f18" width="48%" alt="Trayectoria Final en MATLAB" />
</p>

Como se observa en la comparativa, la estrategia de control implementada fue un éxito. El algoritmo *Pure Pursuit* logró replicar la silueta orgánica del felino, manteniendo la suavidad en las curvas complejas (como los contornos del rostro y las orejas) y evitando movimientos en "zig-zag" que habría generado un control de posición estándar. La densidad de puntos y el ajuste del *Lookahead* evitaron cortes abruptos, logrando un dibujo continuo y anatómicamente correcto.
