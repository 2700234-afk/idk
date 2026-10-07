# idk
import pygame
import sys

# Initialize pygame
pygame.init()

# Screen settings
WIDTH = 800
HEIGHT = 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Classic Pong")

# Colors
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)

# Game settings
PADDLE_WIDTH = 15
PADDLE_HEIGHT = 100
BALL_SIZE = 15
PADDLE_SPEED = 6
BALL_SPEED_X = 5
BALL_SPEED_Y = 5

# Paddles
left_paddle = pygame.Rect(
    30,
    HEIGHT // 2 - PADDLE_HEIGHT // 2,
    PADDLE_WIDTH,
    PADDLE_HEIGHT
)

right_paddle = pygame.Rect(
    WIDTH - 45,
    HEIGHT // 2 - PADDLE_HEIGHT // 2,
    PADDLE_WIDTH,
    PADDLE_HEIGHT
)

# Ball
ball = pygame.Rect(
    WIDTH // 2 - BALL_SIZE // 2,
    HEIGHT // 2 - BALL_SIZE // 2,
    BALL_SIZE,
    BALL_SIZE
)

ball_dx = BALL_SPEED_X
ball_dy = BALL_SPEED_Y

# Scores
left_score = 0
right_score = 0

# Font
font = pygame.font.Font(None, 74)

# Clock
clock = pygame.time.Clock()


def reset_ball():
    global ball_dx, ball_dy

    ball.center = (WIDTH // 2, HEIGHT // 2)

    ball_dx = BALL_SPEED_X if ball_dx < 0 else -BALL_SPEED_X
    ball_dy = BALL_SPEED_Y if ball_dy < 0 else -BALL_SPEED_Y


# Main game loop
while True:
    # Handle events
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    # Keyboard input
    keys = pygame.key.get_pressed()

    # Left paddle: W and S
    if keys[pygame.K_w]:
        left_paddle.y -= PADDLE_SPEED

    if keys[pygame.K_s]:
        left_paddle.y += PADDLE_SPEED

    # Right paddle: Up and Down arrows
    if keys[pygame.K_UP]:
        right_paddle.y -= PADDLE_SPEED

    if keys[pygame.K_DOWN]:
        right_paddle.y += PADDLE_SPEED

    # Keep paddles on screen
    left_paddle.y = max(0, min(HEIGHT - PADDLE_HEIGHT, left_paddle.y))
    right_paddle.y = max(0, min(HEIGHT - PADDLE_HEIGHT, right_paddle.y))

    # Move ball
    ball.x += ball_dx
    ball.y += ball_dy

    # Ball hits top/bottom
    if ball.top <= 0 or ball.bottom >= HEIGHT:
        ball_dy *= -1

    # Ball hits paddles
    if ball.colliderect(left_paddle) and ball_dx < 0:
        ball.left = left_paddle.right
        ball_dx *= -1

    if ball.colliderect(right_paddle) and ball_dx > 0:
        ball.right = right_paddle.left
        ball_dx *= -1

    # Ball goes past left side
    if ball.left <= 0:
        right_score += 1
        reset_ball()

    # Ball goes past right side
    if ball.right >= WIDTH:
        left_score += 1
        reset_ball()

    # Draw everything
    screen.fill(BLACK)

    # Center line
    for y in range(0, HEIGHT, 30):
        pygame.draw.rect(screen, WHITE, (WIDTH // 2 - 2, y, 4, 15))

    # Paddles
    pygame.draw.rect(screen, WHITE, left_paddle)
    pygame.draw.rect(screen, WHITE, right_paddle)

    # Ball
    pygame.draw.rect(screen, WHITE, ball)

    # Scores
    left_text = font.render(str(left_score), True, WHITE)
    right_text = font.render(str(right_score), True, WHITE)

    screen.blit(left_text, (WIDTH // 2 - 100, 30))
    screen.blit(right_text, (WIDTH // 2 + 60, 30))

    # Update screen
    pygame.display.flip()

    # 60 FPS
    clock.tick(60)
