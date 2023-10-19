import pygame
import sys
import random

pygame.init()

# Constants
SCREEN_WIDTH, SCREEN_HEIGHT = 800, 600
WHITE = (255, 255, 255)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# Initialize screen
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Collect the Blue Squares!")

# Initialize player
player_size = 50
player_pos = [SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2]

# Initialize objects and enemies
object_size = 30
num_objects = 15  # Increased number of objects
objects = [[random.randint(0, SCREEN_WIDTH - object_size), random.randint(0, SCREEN_HEIGHT - object_size)] for _ in range(num_objects)]
enemy_size = 30
num_enemies = 8  # Increased number of enemies
enemies = [[random.randint(0, SCREEN_WIDTH - enemy_size), random.randint(0, SCREEN_HEIGHT - enemy_size)] for _ in range(num_enemies)]

# Initialize game variables
score = 0
level = 1
time_remaining = 10000  # Reduced time limit to 20 seconds

font = pygame.font.Font(None, 36)
text_color = (0, 0, 0)

clock = pygame.time.Clock()

# Main game loop
while level <= 3:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    keys = pygame.key.get_pressed()

    # Move the player
    if keys[pygame.K_LEFT] and player_pos[0] > 0:
        player_pos[0] -= 7  # Increased player speed
    if keys[pygame.K_RIGHT] and player_pos[0] < SCREEN_WIDTH - player_size:
        player_pos[0] += 7  # Increased player speed
    if keys[pygame.K_UP] and player_pos[1] > 0:
        player_pos[1] -= 7  # Increased player speed
    if keys[pygame.K_DOWN] and player_pos[1] < SCREEN_HEIGHT - player_size:
        player_pos[1] += 7  # Increased player speed

    # Check for collisions with objects
    for obj in objects:
        if player_pos[0] < obj[0] + object_size and player_pos[0] + player_size > obj[0] and \
           player_pos[1] < obj[1] + object_size and player_pos[1] + player_size > obj[1]:
            objects.remove(obj)
            score += 10

    # Check for collisions with enemies
    for enemy in enemies:
        if player_pos[0] < enemy[0] + enemy_size and player_pos[0] + player_size > enemy[0] and \
           player_pos[1] < enemy[1] + enemy_size and player_pos[1] + player_size > enemy[1]:
            print("Game Over! You collided with an enemy.")
            pygame.quit()
            sys.exit()

    # Draw everything
    screen.fill(WHITE)
    pygame.draw.rect(screen, BLUE, player_pos + [player_size, player_size])

    for obj in objects:
        pygame.draw.rect(screen, BLUE, obj + [object_size, object_size])

    for enemy in enemies:
        pygame.draw.rect(screen, RED, enemy + [enemy_size, enemy_size])

    # Draw score and time remaining
    score_text = font.render("Score: " + str(score), True, text_color)
    screen.blit(score_text, (10, 10))

    time_text = font.render("Time: " + str(time_remaining), True, text_color)
    screen.blit(time_text, (SCREEN_WIDTH - 150, 10))

    pygame.display.flip()

    # Check for win condition
    if len(objects) == 0:
        print("Level " + str(level) + " completed! Your score: " + str(score))
        level += 1
        score = 0
        time_remaining = 20  # Reset time limit for the next level
        objects = [[random.randint(0, SCREEN_WIDTH - object_size), random.randint(0, SCREEN_HEIGHT - object_size)] for _ in range(num_objects)]
        enemies = [[random.randint(0, SCREEN_WIDTH - enemy_size), random.randint(0, SCREEN_HEIGHT - enemy_size)] for _ in range(num_enemies)]

    # Countdown timer
    if time_remaining > 0:
        time_remaining -= 1
    else:
        print("Time's up! You lose.")
        pygame.quit()
        sys.exit()

    # Limit frames per second
    clock.tick(50)
