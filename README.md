# shootergane-java-
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


