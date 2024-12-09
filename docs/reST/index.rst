import pygame
import random
import time

# Initialize Pygame
pygame.init()

# Game constants
WIDTH, HEIGHT = 800, 600
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
FPS = 60

# Initialize the screen
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("MotM Battle Game")

# Player class
class Player(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((50, 50))
        self.image.fill(WHITE)
        self.rect = self.image.get_rect()
        self.rect.center = (x, y)
        self.health = 100
        self.energy = 50
        self.speed = 5
        self.weapon = "Sword"

    def update(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            self.rect.x -= self.speed
        if keys[pygame.K_RIGHT]:
            self.rect.x += self.speed
        if keys[pygame.K_UP]:
            self.rect.y -= self.speed
        if keys[pygame.K_DOWN]:
            self.rect.y += self.speed

    def attack(self, target):
        if self.weapon == "Sword":
            damage = 20
        elif self.weapon == "Gun":
            damage = 30
        else:
            damage = 10
        target.health -= damage
        print(f"Attacked! Target's health is now {target.health}.")

# Enemy class
class Enemy(pygame.sprite.Sprite):
    def __init__(self, x, y, health=100):
        super().__init__()
        self.image = pygame.Surface((50, 50))
        self.image.fill(BLACK)
        self.rect = self.image.get_rect()
        self.rect.center = (x, y)
        self.health = health

    def update(self):
        self.rect.x += random.choice([-1, 1]) * random.randint(1, 3)
        self.rect.y += random.choice([-1, 1]) * random.randint(1, 3)

    def is_alive(self):
        return self.health > 0

# Potion class
class Potion(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((20, 20))
        self.image.fill((0, 255, 0))  # Green potion
        self.rect = self.image.get_rect()
        self.rect.center = (x, y)

    def apply(self, player):
        # Apply potion effect
        player.health += 20
        player.energy += 10
        print(f"Potion applied! Health: {player.health}, Energy: {player.energy}.")

# Main game function
def main():
    clock = pygame.time.Clock()
    running = True

    # Create player and enemies
    player = Player(WIDTH // 2, HEIGHT // 2)
    enemy = Enemy(random.randint(0, WIDTH), random.randint(0, HEIGHT))
    all_sprites = pygame.sprite.Group(player, enemy)

    # Create a potion
    potion = Potion(random.randint(50, WIDTH - 50), random.randint(50, HEIGHT - 50))
    all_sprites.add(potion)

    # Game loop
    while running:
        clock.tick(FPS)

        # Handle events
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    player.attack(enemy)

        # Update sprites
        all_sprites.update()

        # Collision with potion
        if pygame.sprite.collide_rect(player, potion):
            potion.apply(player)
            all_sprites.remove(potion)  # Remove potion after use

        # Clear screen and redraw
        screen.fill((0, 0, 255))  # Blue background
        all_sprites.draw(screen)

        # Display player stats
        font = pygame.font.SysFont('Arial', 24)
        health_text = font.render(f"Health: {player.health}", True, (255, 255, 255))
        screen.blit(health_text, (10, 10))

        # Check if enemy is defeated
        if not enemy.is_alive():
            print("Enemy defeated!")
            running = False  # End the game after defeating the enemy

        pygame.display.flip()

    pygame.quit()

if __name__ == "__main__":
    main()
