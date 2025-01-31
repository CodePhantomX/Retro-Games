import pygame
import random
import sys

# Initialize Pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Pong & Tetris")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# Fonts
font = pygame.font.SysFont("comicsans", 40)

# Clock
clock = pygame.time.Clock()

# Main Menu
def main_menu():
    while True:
        screen.fill(BLACK)
        title = font.render("Select a Game", True, WHITE)
        pong_text = font.render("1. Pong", True, WHITE)
        tetris_text = font.render("2. Tetris", True, WHITE)
        screen.blit(title, (WIDTH // 2 - title.get_width() // 2, 100))
        screen.blit(pong_text, (WIDTH // 2 - pong_text.get_width() // 2, 300))
        screen.blit(tetris_text, (WIDTH // 2 - tetris_text.get_width() // 2, 400))

        pygame.display.update()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_1:
                    pong()
                if event.key == pygame.K_2:
                    tetris()

# Pong Game
def pong():
    # Paddle and ball settings
    paddle_width, paddle_height = 10, 100
    ball_size = 20
    paddle_speed = 5
    ball_speed_x, ball_speed_y = 4, 4

    # Paddle positions
    left_paddle = pygame.Rect(30, HEIGHT // 2 - paddle_height // 2, paddle_width, paddle_height)
    right_paddle = pygame.Rect(WIDTH - 30 - paddle_width, HEIGHT // 2 - paddle_height // 2, paddle_width, paddle_height)

    # Ball position
    ball = pygame.Rect(WIDTH // 2 - ball_size // 2, HEIGHT // 2 - ball_size // 2, ball_size, ball_size)

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

        # Paddle movement
        keys = pygame.key.get_pressed()
        if keys[pygame.K_w] and left_paddle.top > 0:
            left_paddle.y -= paddle_speed
        if keys[pygame.K_s] and left_paddle.bottom < HEIGHT:
            left_paddle.y += paddle_speed
        if keys[pygame.K_UP] and right_paddle.top > 0:
            right_paddle.y -= paddle_speed
        if keys[pygame.K_DOWN] and right_paddle.bottom < HEIGHT:
            right_paddle.y += paddle_speed

        # Ball movement
        ball.x += ball_speed_x
        ball.y += ball_speed_y

        # Ball collision with walls
        if ball.top <= 0 or ball.bottom >= HEIGHT:
            ball_speed_y *= -1
        if ball.left <= 0 or ball.right >= WIDTH:
            ball_speed_x *= -1

        # Ball collision with paddles
        if ball.colliderect(left_paddle) or ball.colliderect(right_paddle):
            ball_speed_x *= -1

        # Drawing
        screen.fill(BLACK)
        pygame.draw.rect(screen, WHITE, left_paddle)
        pygame.draw.rect(screen, WHITE, right_paddle)
        pygame.draw.ellipse(screen, RED, ball)
        pygame.draw.aaline(screen, WHITE, (WIDTH // 2, 0), (WIDTH // 2, HEIGHT))

        pygame.display.update()
        clock.tick(60)

# Tetris Game
def tetris():
    # Tetris settings
    block_size = 30
    grid_width, grid_height = 10, 20
    play_width = block_size * grid_width
    play_height = block_size * grid_height
    top_left_x = (WIDTH - play_width) // 2
    top_left_y = HEIGHT - play_height

    shapes = [
        [[1, 1, 1, 1]],  # I
        [[1, 1, 1], [0, 1, 0]],  # T
        [[1, 1], [1, 1]],  # O
        [[1, 1, 0], [0, 1, 1]],  # S
        [[0, 1, 1], [1, 1, 0]],  # Z
        [[1, 1, 1], [1, 0, 0]],  # L
        [[1, 1, 1], [0, 0, 1]],  # J
    ]

    def create_grid(locked_positions={}):
        grid = [[BLACK for _ in range(grid_width)] for _ in range(grid_height)]
        for y in range(grid_height):
            for x in range(grid_width):
                if (x, y) in locked_positions:
                    grid[y][x] = locked_positions[(x, y)]
        return grid

    def draw_grid(surface, grid):
        for y in range(grid_height):
            for x in range(grid_width):
                pygame.draw.rect(surface, grid[y][x], (top_left_x + x * block_size, top_left_y + y * block_size, block_size, block_size), 0)
        for x in range(grid_width):
            pygame.draw.line(surface, WHITE, (top_left_x + x * block_size, top_left_y), (top_left_x + x * block_size, top_left_y + play_height))
        for y in range(grid_height):
            pygame.draw.line(surface, WHITE, (top_left_x, top_left_y + y * block_size), (top_left_x + play_width, top_left_y + y * block_size))

    def draw_window(surface, grid):
        surface.fill(BLACK)
        draw_grid(surface, grid)
        pygame.display.update()

    locked_positions = {}
    grid = create_grid(locked_positions)

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

        draw_window(screen, grid)
        clock.tick(60)

# Run the main menu
main_menu()
