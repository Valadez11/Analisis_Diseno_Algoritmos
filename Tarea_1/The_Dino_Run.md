# Algoritmo del Dinosaurio de Chrome

El algoritmo del dinosaurio de Chrome (oficialmente llamado **"The Dino Run"**) no es una inteligencia artificial compleja, sino un **bucle de juego (game loop)** tradicional escrito en JavaScript. Su lógica se basa en:

- Física simple  
- Generación aleatoria de obstáculos  
- Incremento gradual de la dificultad  

---

## 1. La Lógica Detrás del Código

El juego se divide en **tres grandes componentes o "clases"**:

---

### A. El Game Loop (El motor)

Es un ciclo infinito (usando `requestAnimationFrame`) que mantiene el juego vivo.  
Si el ciclo se detiene, el juego se congela.

Funciones principales del ciclo:

1. Borra el dibujo anterior.  
2. Calcula las nuevas posiciones (Dino y obstáculos).  
3. Verifica colisiones.  
4. Dibuja todo de nuevo.  

---

### B. El Dinosaurio (El Jugador)

El Dino tiene propiedades como:

- `x`
- `y`
- `vy` → velocidad vertical
- `jumpForce`

**Salto:**  
Cuando presionas la tecla, `vy` se vuelve un número negativo alto (sube).

**Gravedad:**  
En cada frame se suma un valor constante a `vy`, lo que provoca que eventualmente el Dino deje de subir y caiga.

---

### C. El Gestor de Obstáculos

- Usa un **Array (lista)**.
- El algoritmo decide cuándo generar un nuevo obstáculo basándose en:
  - Probabilidad aleatoria
  - Distancia del último obstáculo creado
- Cuando un obstáculo sale de la pantalla por la izquierda, el algoritmo lo elimina del array para **ahorrar memoria**.

---

## Código Simplificado del Algoritmo (Ejemplo Funcional)

El siguiente ejemplo muestra una versión simplificada del algoritmo del juego usando **HTML + JavaScript + Canvas**, donde se implementan:

- Game Loop
- Física de salto
- Obstáculos
- Colisiones
- Reinicio de partida

```html
<!DOCTYPE html>
<html>
<head>
    <title>Mini Dino Clone</title>
    <style>
        canvas { border: 2px solid black; background: #f7f7f7; }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="600" height="200"></canvas>
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        // Variables del Dino
        let dino = { x: 50, y: 150, w: 40, h: 40, vy: 0, grounded: false };
        let gravity = 0.6;
        let jumpForce = -12;

        // Obstáculos
        let obstacles = [];
        let frame = 0;

        function update() {
            frame++;
            // Gravedad y suelo
            dino.vy += gravity;
            dino.y += dino.vy;
            if (dino.y > 150) {
                dino.y = 150;
                dino.vy = 0;
                dino.grounded = true;
            }

            // Generar obstáculos cada 100 frames
            if (frame % 100 === 0) {
                obstacles.push({ x: 600, w: 20, h: 40 });
            }

            // Mover obstáculos y detectar colisión
            obstacles.forEach((obs, index) => {
                obs.x -= 5;

                // Colisión simple (AABB)
                if (dino.x < obs.x + obs.w && dino.x + dino.w > obs.x &&
                    dino.y < 150 + obs.h && dino.y + dino.h > 150) {
                    alert("Game Over!");
                    resetGame();
                }

                if (obs.x < -20) obstacles.splice(index, 1);
            });

            draw();
            requestAnimationFrame(update);
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            // Dibujar Dino
            ctx.fillStyle = "green";
            ctx.fillRect(dino.x, dino.y, dino.w, dino.h);
            // Dibujar Suelo
            ctx.beginPath(); ctx.moveTo(0, 190); ctx.lineTo(600, 190); ctx.stroke();
            // Dibujar Obstáculos
            ctx.fillStyle = "red";
            obstacles.forEach(obs => ctx.fillRect(obs.x, 150, obs.w, obs.h));
        }

        function resetGame() {
            obstacles = [];
            frame = 0;
        }

        window.addEventListener("keydown", (e) => {
            if (e.code === "Space" && dino.grounded) {
                dino.vy = jumpForce;
                dino.grounded = false;
            }
        });

        update();
    </script>
</body>
</html>
