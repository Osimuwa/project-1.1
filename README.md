# project-1.1
import pygame
import sys
import random

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 800, 600
WHITE = (255, 255, 255)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)
PLAYER_SPEED = 5
BULLET_SPEED = 10
ENEMY_SPEED = 2
ENEMY_FREQUENCY = 100  # Adjust this for controlling how often new enemies appear

# Create a window
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Space Invaders")

# Game variables
player_x = WIDTH // 2
player_y = HEIGHT - 50
player_width = 50
player_height = 50
player_color = BLUE

enemies = []
enemy_width = 50
enemy_height = 50
enemy_color = RED

bullets = []
bullet_width = 5
bullet_height = 20
bullet_color = GREEN

# Timer variables
bullet_timer = 0
enemy_timer = ENEMY_FREQUENCY

# Functions
def draw_player():
    pygame.draw.rect(screen, player_color, (player_x, player_y, player_width, player_height))

def draw_enemies():
    for enemy in enemies:
        pygame.draw.rect(screen, enemy_color, (enemy[0], enemy[1], enemy_width, enemy_height))

def draw_bullets():
    for bullet in bullets:
        pygame.draw.rect(screen, bullet_color, (bullet[0], bullet[1], bullet_width, bullet_height))

def move_enemies():
    for enemy in enemies:
        enemy[1] += ENEMY_SPEED

def fire_bullet():
    if pygame.time.get_ticks() - bullet_timer > 300:  # Control bullet firing rate
        bullet_x = player_x + player_width // 2 - bullet_width // 2
        bullet_y = player_y
        bullets.append([bullet_x, bullet_y])
        bullet_timer = pygame.time.get_ticks()

def check_collision():
    for bullet in bullets:
        for enemy in enemies:
            if (
                bullet[0] < enemy[0] + enemy_width and
                bullet[0] + bullet_width > enemy[0] and
                bullet[1] < enemy[1] + enemy_height and
                bullet[1] + bullet_height > enemy[1]
            ):
                bullets.remove(bullet)
                enemies.remove(enemy)

def game_over():
    font = pygame.font.Font(None, 36)
    text = font.render("Game Over", True, RED)
    text_rect = text.get_rect()
    text_rect.center = (WIDTH // 2, HEIGHT // 2)
    screen.blit(text, text_rect)
    pygame.display.flip()
    pygame.time.delay(2000)

# Main game loop
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                player_x -= PLAYER_SPEED
            if event.key == pygame.K_RIGHT:
                player_x += PLAYER_SPEED
            if event.key == pygame.K_SPACE:
                fire_bullet()

    player_x = max(0, min(player_x, WIDTH - player_width))

    enemy_timer -= 1
    if enemy_timer <= 0:
        enemy_x = random.randint(0, WIDTH - enemy_width)
        enemies.append([enemy_x, 0])
        enemy_timer = ENEMY_FREQUENCY

    move_enemies()
    check_collision()

    for enemy in enemies:
        if enemy[1] > HEIGHT:
            game_over()
            running = False

    # Clear the screen
    screen.fill(WHITE)

    draw_player()
    draw_enemies()
    draw_bullets()

    # Update the display
    pygame.display.flip()

# Quit Pygame
pygame.quit()
sys.exit()

