import pygame
import random
import sys

# Initialize Pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 480, 640
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("City Rush: Coin Run")
clock = pygame.time.Clock()

# Colors
GRAY = (50, 50, 50)
YELLOW = (255, 215, 0)
RED = (200, 0, 0)
WHITE = (255, 255, 255)
LANE_COLOR = (200, 200, 200)

# Car setup
car_width, car_height = 40, 70
car_x = WIDTH // 2 - car_width // 2
car_y = HEIGHT - car_height - 20
car_speed = 5

# Coin setup
coins = []
coin_size = 20
score = 0

# Font setup
font = pygame.font.SysFont(None, 36)

# Function to draw road lines
def draw_road():
    screen.fill(GRAY)
    for x in range(50, WIDTH, 100):
        pygame.draw.line(screen, LANE_COLOR, (x, 0), (x, HEIGHT), 2)

# Function to draw the car
def draw_car(x, y):
    pygame.draw.rect(screen, RED, (x, y, car_width, car_height))

# Function to draw coins
def draw_coins():
    for coin in coins:
        pygame.draw.circle(screen, YELLOW, (coin[0], coin[1]), coin_size // 2)

# Function to spawn a new coin
def spawn_coin():
    x = random.randint(0, WIDTH - coin_size)
    y = -coin_size
    coins.append([x, y])

# Main loop
running = True
SPAWNCOIN = pygame.USEREVENT + 1
pygame.time.set_timer(SPAWNCOIN, 1000)

while running:
    clock.tick(60)
    draw_road()

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == SPAWNCOIN:
            spawn_coin()

    # Move car
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and car_x > 0:
        car_x -= car_speed
    if keys[pygame.K_RIGHT] and car_x < WIDTH - car_width:
        car_x += car_speed

    # Update and draw coins
    for coin in coins[:]:
        coin[1] += 4
        if coin[1] > HEIGHT:
            coins.remove(coin)
        elif car_x < coin[0] < car_x + car_width and car_y < coin[1] < car_y + car_height:
            coins.remove(coin)
            score += 1

    draw_car(car_x, car_y)
    draw_coins()

    # Draw score
    score_text = font.render(f"Score: {score}", True, WHITE)
    screen.blit(score_text, (10, 10))

    pygame.display.flip()

pygame.quit()
sys.exit()
