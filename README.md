# Juego de Palabras - Explicación del Proyecto

## Descripción General
Este es un juego de mecanografía en el que las palabras caen desde la parte superior de la pantalla y el jugador debe escribirlas correctamente antes de que lleguen al fondo. Si una palabra no es eliminada a tiempo, el jugador pierde puntos. La dificultad aumenta con el tiempo, haciendo que las palabras aparezcan más rápido y con mayor complejidad.

El juego también incluye una **tabla de puntuaciones** (Leaderboard) que se actualiza en tiempo real utilizando un servidor WebSocket local, permitiendo que varios jugadores en la misma red vean y comparen sus puntuaciones.

---

## Características Principales

### 1. Generación Aleatoria de Palabras
- Se generan palabras de diferentes longitudes y con distintos caracteres, incluyendo letras y símbolos.
- La dificultad aumenta progresivamente, introduciendo palabras más largas y con caracteres especiales.

### 2. Mecánica del Juego
- Las palabras caen a una velocidad determinada.
- El jugador debe escribir la palabra exacta en un campo de entrada para eliminarla y ganar puntos.
- Si una palabra llega al final de la pantalla, el jugador pierde puntos.

### 3. Dificultad Progresiva
- A medida que el tiempo avanza, las palabras aparecen con mayor frecuencia.
- La velocidad de caída de las palabras aumenta, pero con un límite máximo.
- Se introducen palabras más largas y complejas con caracteres especiales.

### 4. Puntuación y Tabla de Posiciones
- Cada palabra eliminada correctamente otorga puntos.
- Si una palabra alcanza la parte inferior de la pantalla, se restan puntos.
- Al finalizar el juego, el jugador puede registrar su puntuación, que se guarda en un servidor WebSocket y se muestra en una tabla de posiciones.

### 5. Conectividad con Servidor WebSocket
- La puntuación se almacena en un servidor WebSocket local (sin conexión a la nube).
- Permite que varios jugadores en la misma red compartan y vean sus puntuaciones en tiempo real.

### 6. Interfaz Gráfica Mejorada con Bootstrap
- Se utiliza Bootstrap para mejorar el diseño y la responsividad del juego.
- La tabla de posiciones se ha estilizado para que los primeros lugares resalten más.
- Se ha agregado un sistema de colores aleatorios para el fondo del juego cuando se acierta una palabra.

### 7. Opciones de Control
- **Iniciar Juego**: Comienza la aparición de palabras.
- **Detener Juego**: Finaliza la partida y permite registrar la puntuación.
- **Entrada de Texto**: Permite al jugador escribir palabras para eliminarlas.

---

## Estructura del Código

### 1. HTML (Interfaz)
- Contiene la estructura del juego, incluyendo:
  - Área de juego donde caen las palabras.
  - Input donde el jugador escribe.
  - Botones para iniciar y detener el juego.
  - Tabla de puntuaciones en la parte superior derecha.

### 2. CSS (Diseño y Animaciones)
- Define el estilo de la página.
- Incluye una animación para que las palabras caigan suavemente.
- Se usa Bootstrap para mejorar la responsividad.

### 3. JavaScript (Lógica del Juego)
- **Generación de Palabras**:
  - Se crean palabras aleatorias con y sin símbolos.
- **Caída de Palabras**:
  - Se asigna una posición aleatoria a cada palabra.
  - Se anima la caída con `@keyframes`.
- **Dificultad Progresiva**:
  - Se controla la frecuencia de aparición y la velocidad de caída de las palabras.
- **Manejo de Puntuación**:
  - Si el jugador escribe una palabra correctamente, gana puntos y la palabra desaparece.
  - Si una palabra llega al fondo, se restan puntos.
- **Conexión con Servidor WebSocket**:
  - Se envían y reciben datos para actualizar la tabla de posiciones en tiempo real.

---

## Tecnologías Utilizadas
- **HTML5**: Estructura del juego.
- **CSS3 (Bootstrap)**: Diseño responsivo y animaciones.
- **JavaScript (DOM, Eventos, WebSockets)**: Lógica del juego y conexión con el servidor.
- **WebSockets (Servidor Node.js con `ws`)**: Comunicación en tiempo real para la tabla de puntuaciones.

---

## Mejoras Futuras
- Implementar un sistema de niveles o rondas.
- Agregar efectos visuales y sonoros para mayor inmersión.
- Guardar datos en una base de datos local para persistencia de puntuaciones.
- Permitir diferentes modos de juego con distintas reglas.

---

Este resumen te ayudará a comprender el código y a realizar futuras mejoras. Si necesitas más detalles sobre alguna parte, dime y te lo explico con más profundidad. 🚀

## Instalación y Configuración

### 1. Instalación de Dependencias
Para ejecutar este juego, necesitas tener instalado **Node.js** y **npm**. Si aún no los tienes, instálalos desde [nodejs.org](https://nodejs.org/).

```sh
# Verifica que Node.js está instalado
node -v

# Verifica que npm está instalado
npm -v
```

### 2. Configuración del Servidor WebSocket
El juego se conecta a un servidor WebSocket que gestiona la tabla de posiciones. Sigue estos pasos para configurarlo:

1. Crea un nuevo directorio para el servidor:
   ```sh
   mkdir servidor-palabras && cd servidor-palabras
   ```
2. Inicializa un proyecto de Node.js:
   ```sh
   npm init -y
   ```
3. Instala **ws** para WebSockets:
   ```sh
   npm install ws
   ```
4. Crea un archivo `server.js` con el siguiente contenido:
   ```js
   const WebSocket = require('ws');

   const server = new WebSocket.Server({ port: 8080 });
   let leaderboard = [];

   server.on('connection', socket => {
       socket.send(JSON.stringify({ type: 'leaderboard', leaderboard }));
       
       socket.on('message', message => {
           const data = JSON.parse(message);
           if (data.type === 'new-score') {
               leaderboard.push({ name: data.name, score: data.score });
               leaderboard.sort((a, b) => b.score - a.score);
               server.clients.forEach(client => {
                   if (client.readyState === WebSocket.OPEN) {
                       client.send(JSON.stringify({ type: 'leaderboard', leaderboard }));
                   }
               });
           }
       });
   });
   ```

### 3. Ejecutar el Servidor
Para iniciar el servidor WebSocket, usa:
```sh
node server.js
```

Este comando iniciará el servidor en el puerto `8080`. Asegúrate de que esté corriendo antes de abrir el juego en el navegador.

### 4. Ejecutar el Juego
Abre el archivo `index.html` en tu navegador para comenzar a jugar.
