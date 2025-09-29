we# shootergane-java-
juego 2D estilo arcade en java con power-ups, niiveles y graficos
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

public class ShooterGame extends JPanel implements KeyListener, ActionListener {
    private int playerX = 250; // posición inicial del jugador
    private final int PLAYER_Y = 500;
    private Timer timer;

    public ShooterGame() {
        setPreferredSize(new Dimension(600, 600));
        setBackground(Color.black);
        setFocusable(true);
        addKeyListener(this);

        timer = new Timer(10, this); // refresco cada 10ms
        timer.start();
    }

    public void paintComponent(Graphics g) {
        super.paintComponent(g);
        // dibujar jugador
        g.setColor(Color.green);
        g.fillRect(playerX, PLAYER_Y, 50, 20);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        repaint(); // refresca pantalla
    }

    @Override
    public void keyPressed(KeyEvent e) {
        int key = e.getKeyCode();
        if (key == KeyEvent.VK_LEFT && playerX > 0) {
            playerX -= 10;
        }
        if (key == KeyEvent.VK_RIGHT && playerX < getWidth() - 50) {
            playerX += 10;
        }
    }

    @Override public void keyReleased(KeyEvent e) {}
    @Override public void keyTyped(KeyEvent e) {}

    public static void main(String[] args) {
        JFrame frame = new JFrame("Shooter Game");
        ShooterGame game = new ShooterGame();
        frame.add(game);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.pack();
        frame.setVisible(true);
        frame.setLocationRelativeTo(null);
    }
}
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;

public class ShooterGame extends JPanel implements KeyListener, ActionListener {
    private int playerX = 250;
    private final int PLAYER_Y = 500;
    private Timer timer;

    // Lista de balas activas
    private ArrayList<Rectangle> bullets;

    public ShooterGame() {
        setPreferredSize(new Dimension(600, 600));
        setBackground(Color.black);
        setFocusable(true);
        addKeyListener(this);

        bullets = new ArrayList<>();
        timer = new Timer(10, this); // refresco cada 10ms
        timer.start();
    }

    public void paintComponent(Graphics g) {
        super.paintComponent(g);
        // Dibujar jugador
        g.setColor(Color.green);
        g.fillRect(playerX, PLAYER_Y, 50, 20);

        // Dibujar balas
        g.setColor(Color.yellow);
        for (Rectangle bullet : bullets) {
            g.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);
        }
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        // Mover balas hacia arriba
        for (int i = 0; i < bullets.size(); i++) {
            Rectangle b = bullets.get(i);
            b.y -= 5; // velocidad de la bala
            if (b.y < 0) {
                bullets.remove(i);
                i--;
            }
        }
        repaint(); // refresca pantalla
    }

    @Override
    public void keyPressed(KeyEvent e) {
        int key = e.getKeyCode();
        if (key == KeyEvent.VK_LEFT && playerX > 0) {
            playerX -= 10;
        }
        if (key == KeyEvent.VK_RIGHT && playerX < getWidth() - 50) {
            playerX += 10;
        }
        if (key == KeyEvent.VK_SPACE) {
            // Crear nueva bala
            bullets.add(new Rectangle(playerX + 22, PLAYER_Y - 10, 5, 10));
        }
    }

    @Override public void keyReleased(KeyEvent e) {}
    @Override public void keyTyped(KeyEvent e) {}

    public static void main(String[] args) {
        JFrame frame = new JFrame("Shooter Game");
        ShooterGame game = new ShooterGame();
        frame.add(game);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.pack();
        frame.setVisible(true);
        frame.setLocationRelativeTo(null);
    }
}
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;
import java.util.Random;

public class ShooterGame extends JPanel implements KeyListener, ActionListener {
    private int playerX = 250;
    private final int PLAYER_Y = 500;
    private Timer timer;

    private ArrayList<Rectangle> bullets;
    private ArrayList<Rectangle> enemies;
    private int score = 0;
    private Random random;

    public ShooterGame() {
        setPreferredSize(new Dimension(600, 600));
        setBackground(Color.black);
        setFocusable(true);
        addKeyListener(this);

        bullets = new ArrayList<>();
        enemies = new ArrayList<>();
        random = new Random();

        // Crear enemigos iniciales
        for (int i = 0; i < 5; i++) {
            enemies.add(new Rectangle(random.nextInt(550), random.nextInt(100), 50, 20));
        }

        timer = new Timer(20, this); // refresco cada 20ms
        timer.start();
    }

    public void paintComponent(Graphics g) {
        super.paintComponent(g);
        // Dibujar jugador
        g.setColor(Color.green);
        g.fillRect(playerX, PLAYER_Y, 50, 20);

        // Dibujar balas
        g.setColor(Color.yellow);
        for (Rectangle bullet : bullets) {
            g.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);
        }

        // Dibujar enemigos
        g.setColor(Color.red);
        for (Rectangle enemy : enemies) {
            g.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
        }

        // Dibujar puntaje
        g.setColor(Color.white);
        g.setFont(new Font("Arial", Font.BOLD, 18));
        g.drawString("Score: " + score, 10, 20);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        // Mover balas
        for (int i = 0; i < bullets.size(); i++) {
            Rectangle b = bullets.get(i);
            b.y -= 5;
            if (b.y < 0) {
                bullets.remove(i);
                i--;
            }
        }

        // Mover enemigos hacia abajo
        for (Rectangle enemy : enemies) {
            enemy.y += 2; // velocidad de caída
        }

        // Revisar colisiones balas <-> enemigos
        for (int i = 0; i < bullets.size(); i++) {
            Rectangle b = bullets.get(i);
            for (int j = 0; j < enemies.size(); j++) {
                Rectangle enemy = enemies.get(j);
                if (b.intersects(enemy)) {
                    bullets.remove(i);
                    enemies.remove(j);
                    score += 10;
                    i--;
                    break;
                }
            }
        }

        // Revisar si algún enemigo llegó al jugador
        for (Rectangle enemy : enemies) {
            if (enemy.y + enemy.height >= PLAYER_Y) {
                timer.stop();
                JOptionPane.showMessageDialog(this, "GAME OVER! Puntaje: " + score);
                System.exit(0);
            }
        }

        // Generar nuevos enemigos aleatoriamente
        if (enemies.size() < 5) {
            enemies.add(new Rectangle(random.nextInt(550), 0, 50, 20));
        }

        repaint();
    }

    @Override
    public void keyPressed(KeyEvent e) {
        int key = e.getKeyCode();
        if (key == KeyEvent.VK_LEFT && playerX > 0) {
            playerX -= 10;
        }
        if (key == KeyEvent.VK_RIGHT && playerX < getWidth() - 50) {
            playerX += 10;
        }
        if (key == KeyEvent.VK_SPACE) {
            bullets.add(new Rectangle(playerX + 22, PLAYER_Y - 10, 5, 10));
        }
    }

    @Override public void keyReleased(KeyEvent e) {}
    @Override public void keyTyped(KeyEvent e) {}

    public static void main(String[] args) {
        JFrame frame = new JFrame("Shooter Game");
        ShooterGame game = new ShooterGame();
        frame.add(game);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.pack();
        frame.setVisible(true);
        frame.setLocationRelativeTo(null);
    }
}
private int level = 1;
private ArrayList<Rectangle> powerUps;
// Dentro del constructor
powerUps = new ArrayList<>();

// En actionPerformed()
// Subir de nivel cada 100 puntos
if (score / 100 + 1 > level) {
    level++;
    // Aumentar velocidad enemigos
    for (Rectangle enemy : enemies) {
        enemy.y += 1; // sube velocidad gradual
    }
}

// Generar power-up aleatorio
if (random.nextInt(1000) < 2) { // probabilidad baja cada tick
    powerUps.add(new Rectangle(random.nextInt(550), 0, 20, 20));
}

// Mover power-ups hacia abajo
for (int i = 0; i < powerUps.size(); i++) {
    Rectangle pu = powerUps.get(i);
    pu.y += 2; // velocidad del power-up
    if (pu.y > getHeight()) {
        powerUps.remove(i);
        i--;
    }
}

// Revisar colisión jugador <-> power-ups
for (int i = 0; i < powerUps.size(); i++) {
    Rectangle pu = powerUps.get(i);
    Rectangle playerRect = new Rectangle(playerX, PLAYER_Y, 50, 20);
    if (pu.intersects(playerRect)) {
        // efecto del power-up: ejemplo, disparo doble
        powerUps.remove(i);
        i--;
        activateDoubleShot(); // función que activamos (tienes que crear)
    }
}
private boolean doubleShotActive = false;
private int doubleShotTimer = 0; // duración del power-up
private void activateDoubleShot() {
    doubleShotActive = true;
    doubleShotTimer = 500; // duración en ticks (aprox 10 segundos si Timer 20ms)
}
if (key == KeyEvent.VK_SPACE) {
    if (doubleShotActive) {
        // Disparo doble: dos balas
        bullets.add(new Rectangle(playerX + 10, PLAYER_Y - 10, 5, 10));
        bullets.add(new Rectangle(playerX + 30, PLAYER_Y - 10, 5, 10));
    } else {
        bullets.add(new Rectangle(playerX + 22, PLAYER_Y - 10, 5, 10));
    }
}
// En actionPerformed()
if (doubleShotActive) {
    doubleShotTimer--;
    if (doubleShotTimer <= 0) {
        doubleShotActive = false;
    }
}
// Cada 100 puntos, subir de nivel
if (score / 100 + 1 > level) {
    level++;
    // Incrementar velocidad enemigos
    for (Rectangle enemy : enemies) {
        enemy.y += 1; // más rápido
    }
}
// En paintComponent()
g.setColor(Color.white);
g.setFont(new Font("Arial", Font.BOLD, 18));
g.drawString("Score: " + score, 10, 20);
g.drawString("Level: " + level, 500, 20);
if (doubleShotActive) {
    g.setColor(Color.cyan);
    g.drawString("Power-Up: Double Shot", 200, 20);
}
private ArrayList<Point> stars = new ArrayList<>();

// En el constructor, generar estrellas aleatorias
for (int i = 0; i < 50; i++) {
    stars.add(new Point(random.nextInt(600), random.nextInt(600)));
}

// En paintComponent()
g.setColor(Color.white);
for (Point star : stars) {
    g.fillRect(star.x, star.y, 2, 2);
}

// En actionPerformed(), mover estrellas hacia abajo
for (Point star : stars) {
    star.y += 1;
    if (star.y > getHeight()) star.y = 0; // reaparece arriba
}
private ArrayList<Point> explosions = new ArrayList<>();

// Al detectar colisión bala <-> enemigo
explosions.add(new Point(enemy.x, enemy.y));

// En paintComponent()
g.setColor(Color.orange);
for (Point e : explosions) {
    g.fillOval(e.x, e.y, 50, 20);
}
// Limpiar explosiones (opcional: mantener 5 ticks)
explosions.clear();
import javax.sound.sampled.*;
import java.io.File;

// Función para reproducir sonido
private void playSound(String filepath) {
    try {
        AudioInputStream audioInput = AudioSystem.getAudioInputStream(new File(filepath));
        Clip clip = AudioSystem.getClip();
        clip.open(audioInput);
        clip.start();
    } catch (Exception e) {
        e.printStackTrace();
    }
}

// Llamar playSound("disparo.wav") cuando se dispara
// Llamar playSound("explosion.wav") al destruir un enemigo
private Image playerImg;
private Image enemyImg;
private Image powerUpImg;

public ShooterGame() {
    // ... otras inicializaciones ...

    // Cargar imágenes
    playerImg = new ImageIcon("player.png").getImage();
    enemyImg = new ImageIcon("enemy.png").getImage();
    powerUpImg = new ImageIcon("powerup.png").getImage();
}
// Jugador
g.drawImage(playerImg, playerX, PLAYER_Y, 50, 50, this);

// Enemigos
for (Rectangle enemy : enemies) {
    g.drawImage(enemyImg, enemy.x, enemy.y, enemy.width, enemy.height, this);
}

// Power-ups
for (Rectangle pu : powerUps) {
    g.drawImage(powerUpImg, pu.x, pu.y, pu.width, pu.height, this);
}
// Variables principales
private int playerX = 250;
private final int PLAYER_Y = 500;
private Timer timer;

private ArrayList<Rectangle> bullets;
private ArrayList<Rectangle> enemies;
private ArrayList<Rectangle> powerUps;
private ArrayList<Point> stars;
private ArrayList<Point> explosions;

private int score = 0;
private int level = 1;
private boolean doubleShotActive = false;
private int doubleShotTimer = 0;

private Image playerImg;
private Image enemyImg;
private Image powerUpImg;

private Random random;
public ShooterGame() {
    setPreferredSize(new Dimension(600, 600));
    setBackground(Color.black);
    setFocusable(true);
    addKeyListener(this);

    bullets = new ArrayList<>();
    enemies = new ArrayList<>();
    powerUps = new ArrayList<>();
    stars = new ArrayList<>();
    explosions = new ArrayList<>();
    random = new Random();

    // Cargar imágenes
    playerImg = new ImageIcon("player.png").getImage();
    enemyImg = new ImageIcon("enemy.png").getImage();
    powerUpImg = new ImageIcon("powerup.png").getImage();

    // Generar estrellas y enemigos
    for (int i = 0; i < 50; i++) stars.add(new Point(random.nextInt(600), random.nextInt(600)));
    for (int i = 0; i < 5; i++) enemies.add(new Rectangle(random.nextInt(550), random.nextInt(100), 50, 20));

    timer = new Timer(20, this);
    timer.start();
}
@Override
public void paintComponent(Graphics g) {
    super.paintComponent(g);

    // Fondo (estrellas)
    g.setColor(Color.white);
    for (Point star : stars) g.fillRect(star.x, star.y, 2, 2);

    // Jugador
    g.drawImage(playerImg, playerX, PLAYER_Y, 50, 50, this);

    // Enemigos
    for (Rectangle enemy : enemies) g.drawImage(enemyImg, enemy.x, enemy.y, enemy.width, enemy.height, this);

    // Balas
    g.setColor(Color.yellow);
    for (Rectangle bullet : bullets) g.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);

    // Power-ups
    for (Rectangle pu : powerUps) g.drawImage(powerUpImg, pu.x, pu.y, pu.width, pu.height, this);

    // Explosiones
    g.setColor(Color.orange);
    for (Point e : explosions) g.fillOval(e.x, e.y, 50, 20);
    explosions.clear();

    // Texto: puntaje, nivel, power-up
    g.setColor(Color.white);
    g.setFont(new Font("Arial", Font.BOLD, 18));
    g.drawString("Score: " + score, 10, 20);
    g.drawString("Level: " + level, 500, 20);
    if (doubleShotActive) g.drawString("Power-Up: Double Shot", 200, 20);
}
@Override
public void actionPerformed(ActionEvent e) {
    // Mover balas
    for (int i = 0; i < bullets.size(); i++) {
        Rectangle b = bullets.get(i);
        b.y -= 5;
        if (b.y < 0) { bullets.remove(i); i--; }
    }

    // Mover enemigos
    for (Rectangle enemy : enemies) enemy.y += 2 + level; // más rápido por nivel

    // Mover estrellas
    for (Point star : stars) {
        star.y += 1;
        if (star.y > getHeight()) star.y = 0;
    }

    // Colisión balas <-> enemigos
    for (int i = 0; i < bullets.size(); i++) {
        Rectangle b = bullets.get(i);
        for (int j = 0; j < enemies.size(); j++) {
            Rectangle enemy = enemies.get(j);
            if (b.intersects(enemy)) {
                bullets.remove(i);
                enemies.remove(j);
                explosions.add(new Point(enemy.x, enemy.y));
                score += 10;
                i--; break;
            }
        }
    }

    // Colisión jugador <-> power-ups
    Rectangle playerRect = new Rectangle(playerX, PLAYER_Y, 50, 20);
    for (int i = 0; i < powerUps.size(); i++) {
        Rectangle pu = powerUps.get(i);
        if (pu.intersects(playerRect)) {
            powerUps.remove(i); i--;
            activateDoubleShot();
        }
    }

    // Power-up temporal
    if (doubleShotActive) {
        doubleShotTimer--;
        if (doubleShotTimer <= 0) doubleShotActive = false;
    }

    // Generar nuevos enemigos
    if (enemies.size() < 5 + level) enemies.add(new Rectangle(random.nextInt(550), 0, 50, 20));

    // Generar power-ups aleatorios
    if (random.nextInt(1000) < 2) powerUps.add(new Rectangle(random.nextInt(550), 0, 20, 20));

    // Revisar Game Over
    for (Rectangle enemy : enemies) {
        if (enemy.y + enemy.height >= PLAYER_Y) {
            timer.stop();
            JOptionPane.showMessageDialog(this, "GAME OVER! Puntaje: " + score);
            System.exit(0);
        }
    }

    repaint();
}
@Override
public void keyPressed(KeyEvent e) {
    int key = e.getKeyCode();
    if (key == KeyEvent.VK_LEFT && playerX > 0) playerX -= 10;
    if (key == KeyEvent.VK_RIGHT && playerX < getWidth() - 50) playerX += 10;
    if (key == KeyEvent.VK_SPACE) {
        if (doubleShotActive) {
            bullets.add(new Rectangle(playerX + 10, PLAYER_Y - 10, 5, 10));
            bullets.add(new Rectangle(playerX + 30, PLAYER_Y - 10, 5, 10));
        } else {
            bullets.add(new Rectangle(playerX + 22, PLAYER_Y - 10, 5, 10));
        }
    }
}
private void activateDoubleShot() {
    doubleShotActive = true;
    doubleShotTimer = 500; // ticks del Timer
}
public static void main(String[] args) {
    JFrame frame = new JFrame("Shooter Game");
    ShooterGame game = new ShooterGame();
    frame.add(game);
    frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    frame.pack();
    frame.setVisible(true);
    frame.setLocationRelativeTo(null);
}
ShooterGame-Java/
│
├─ src/
│   └─ ShooterGame.java
├─ assets/
│   ├─ player.png
│   ├─ enemy.png
│   └─ powerup.png
└─ README.md
git init
git add .
git commit -m "Primer commit: juego completo Shooter Game en Java"
git branch -M main
git remote add origin https://github.com/tuusuario/ShooterGame-Java.git
git push -u origin main
# Shooter Game - Java Arcade

🎮 Juego 2D estilo arcade creado en Java con Swing.

## Características
- Jugador con movimiento horizontal y disparos
- Enemigos que bajan desde arriba
- Power-ups: disparo doble temporal
- Niveles de dificultad progresiva
- Fondo animado y explosiones visuales
- Puntaje y Game Over

## Controles
- Flecha izquierda / derecha: mover jugador
- Barra espaciadora: disparar

## Instalación
1. Descargar o clonar el repositorio
2. Asegurarse de tener Java 8 o superior
3. Ejecutar `ShooterGame.java` desde tu IDE o terminal

## Capturas

![Screenshot1](assets/screenshot1.png)
![Screenshot2](assets/screenshot2.png)

## Autor
Andrés Zambrano







