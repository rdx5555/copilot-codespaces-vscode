#include <SFML/Graphics.hpp>
#include <SFML/Window.hpp>
#include <SFML/System.hpp>
#include <vector>

// Bullet class
class Bullet {
public:
    sf::RectangleShape shape;
    float speed;

    Bullet(float x, float y) {
        shape.setSize(sf::Vector2f(10.f, 5.f));
        shape.setFillColor(sf::Color::Red);
        shape.setPosition(x, y);
        speed = 10.f;
    }

    void move() {
        shape.move(speed, 0.f);
    }
};

// Player class
class Player {
public:
    sf::RectangleShape shape;
    float speed;

    Player() {
        shape.setSize(sf::Vector2f(50.f, 50.f));
        shape.setFillColor(sf::Color::Green);
        shape.setPosition(50.f, 300.f);
        speed = 5.f;
    }

    void move(const sf::Vector2f& direction) {
        shape.move(direction * speed);
    }
};

// Main function
int main() {
    sf::RenderWindow window(sf::VideoMode(800, 600), "Shooting Game");
    window.setFramerateLimit(60);

    Player player;
    std::vector<Bullet> bullets;

    while (window.isOpen()) {
        sf::Event event;
        while (window.pollEvent(event)) {
            if (event.type == sf::Event::Closed) {
                window.close();
            }
        }

        // Player controls
        sf::Vector2f direction(0.f, 0.f);
        if (sf::Keyboard::isKeyPressed(sf::Keyboard::W))
            direction.y = -1.f;
        if (sf::Keyboard::isKeyPressed(sf::Keyboard::S))
            direction.y = 1.f;
        if (sf::Keyboard::isKeyPressed(sf::Keyboard::A))
            direction.x = -1.f;
        if (sf::Keyboard::isKeyPressed(sf::Keyboard::D))
            direction.x = 1.f;

        player.move(direction);

        // Shooting bullets
        if (sf::Keyboard::isKeyPressed(sf::Keyboard::Space)) {
            bullets.emplace_back(player.shape.getPosition().x + 50.f, player.shape.getPosition().y + 20.f);
        }

        // Move bullets
        for (auto& bullet : bullets) {
            bullet.move();
        }

        // Remove off-screen bullets
        bullets.erase(std::remove_if(bullets.begin(), bullets.end(),
                                     [](const Bullet& bullet) { return bullet.shape.getPosition().x > 800.f; }),
                      bullets.end());

        // Render
        window.clear();
        window.draw(player.shape);
        for (const auto& bullet : bullets) {
            window.draw(bullet.shape);
        }
        window.display();
    }

    return 0;
}
