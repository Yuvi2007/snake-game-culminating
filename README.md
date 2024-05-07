package com.mycompany.javasnakegame;
 
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;
import java.util.Random;

public class JavaSnakeGame extends JPanel implements KeyListener, ActionListener {

    private final int WIDTH = 600;
    private final int HEIGHT = 600;
    private final int UNIT_SIZE = 25;
    private final int GAME_UNITS = (WIDTH * HEIGHT) / (UNIT_SIZE * UNIT_SIZE);
    private final int DELAY = 75;
    private ArrayList<Point> snake;
    private ArrayList<Point> foods;
    private char direction = 'R';
    private boolean running = false;
    private Timer timer;
    private int score = 0;

    public JavaSnakeGame() {
        this.setPreferredSize(new Dimension(WIDTH, HEIGHT));
        this.setBackground(Color.BLACK);
        this.setFocusable(true);
        this.addKeyListener(this);
        snake = new ArrayList<>();
        foods = new ArrayList<>();
        startGame();
    }

    public void startGame() {
        snake.clear();
        foods.clear();
        snake.add(new Point(WIDTH / 2, HEIGHT / 2));
        placeFood();
        running = true;
        timer = new Timer(DELAY, this);
        timer.start();
    }

    public void placeFood() {
        Random rand = new Random();
        int x = rand.nextInt((WIDTH / UNIT_SIZE)) * UNIT_SIZE;
        int y = rand.nextInt((HEIGHT / UNIT_SIZE)) * UNIT_SIZE;
        foods.add(new Point(x, y));
    }

    public void move() {
        if (!running) return;

        Point head = snake.get(0);
        Point newHead = new Point(head);

        switch (direction) {
            case 'U':
                newHead.y -= UNIT_SIZE;
                break;
            case 'D':
                newHead.y += UNIT_SIZE;
                break;
            case 'L':
                newHead.x -= UNIT_SIZE;
                break;
            case 'R':
                newHead.x += UNIT_SIZE;
                break;
        }

        if (newHead.x < 0 || newHead.y < 0 || newHead.x >= WIDTH || newHead.y >= HEIGHT || snake.contains(newHead)) {
            running = false;
        } else {
            snake.add(0, newHead);
            if (!foods.isEmpty()) {
                Point food = foods.get(0);
                if (newHead.equals(food)) {
                    foods.remove(0);
                    placeFood();
                    score++;
                } else {
                    snake.remove(snake.size() - 1);
                }
            }
        }
    }

    public void paintComponent(Graphics g) {
        super.paintComponent(g);
        draw(g);
    }

    public void draw(Graphics g) {
        if (running) {
            for (Point point : foods) {
                g.setColor(Color.RED);
                g.fillRect(point.x, point.y, UNIT_SIZE, UNIT_SIZE);
            }
            for (Point point : snake) {
                g.setColor(Color.GREEN);
                g.fillRect(point.x, point.y, UNIT_SIZE, UNIT_SIZE);
            }
            g.setColor(Color.WHITE);
            g.setFont(new Font("Arial", Font.BOLD, 20));
            g.drawString("Score: " + score, 10, 20);
        } else {
            gameOver(g);
        }
    }

    public void gameOver(Graphics g) {
        g.setColor(Color.RED);
        g.setFont(new Font("Arial", Font.BOLD, 40));
        FontMetrics metrics = getFontMetrics(g.getFont());
        g.drawString("Game Over", (WIDTH - metrics.stringWidth("Game Over")) / 2, HEIGHT / 2);
        g.drawString("Score: " + score, (WIDTH - metrics.stringWidth("Score: " + score)) / 2, HEIGHT / 2 + 50);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (running) {
            move();
        }
        repaint();
    }

    @Override
    public void keyPressed(KeyEvent e) {
        int key = e.getKeyCode();
        switch (key) {
            case KeyEvent.VK_UP:
                if (direction != 'D') {
                    direction = 'U';
                }
                break;
            case KeyEvent.VK_DOWN:
                if (direction != 'U') {
                    direction = 'D';
                }
                break;
            case KeyEvent.VK_LEFT:
                if (direction != 'R') {
                    direction = 'L';
                }
                break;
            case KeyEvent.VK_RIGHT:
                if (direction != 'L') {
                    direction = 'R';
                }
                break;
        }
    }

    @Override
    public void keyReleased(KeyEvent e) {
    }

    @Override
    public void keyTyped(KeyEvent e) {
    }

    public static void main(String[] args) {
        JFrame frame = new JFrame("Java Snake Game");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setResizable(false);
        frame.add(new JavaSnakeGame());
        frame.pack();
        frame.setLocationRelativeTo(null);
        frame.setVisible(true);
    }
}
