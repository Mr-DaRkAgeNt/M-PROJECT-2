# M-PROJECT-2
import pygame
import random

# Initialize pygame library
pygame.init()

# Setting up the display
width, height = 900, 800
window = pygame.display.set_mode((width, height))
pygame.display.set_caption("Shooting The Boxes Game")

# Colors Used in This Game 
white = (255, 255, 255)
black = (0, 0, 0)
red = (255, 0, 0)
Orange = (255, 165, 0)
Green = (0, 255, 0)

# Player Square Box Size
player_size = 50
player_pos = [width // 2, height - 2 * player_size]

# Bullet Physics
bullet_size = 20
bullet_pos = [0, 0]
bullet_state = "ready"  # "ready" means bullet is not visible
bullet_speed = 20  # Increased the bullet speed

# Enemy Boxes Physics
box_size = 50
num_boxes = 5
box_pos = [[random.randint(0, width - box_size), 0] for _ in range(num_boxes)]
box_speed = 5

# Game Motion
clock = pygame.time.Clock()
score = 0
lives = 10 
font = pygame.font.SysFont(None, 35)

# Gaming Functions 
def draw_player(pos):
    pygame.draw.rect(window, Green, (pos[0], pos[1], player_size, player_size))

def draw_bullet(pos):
    pygame.draw.polygon(window, red, [(pos[0] + player_size // 2, pos[1]), 
                                      (pos[0] + player_size // 2 - bullet_size // 2, pos[1] + bullet_size), 
                                      (pos[0] + player_size // 2 + bullet_size // 2, pos[1] + bullet_size)])

def draw_box(pos):
    pygame.draw.rect(window,Orange, (pos[0], pos[1], box_size, box_size))

def show_score(score):
    score_text = font.render(f"Score: {score}", True, white)
    window.blit(score_text, (10, 10))

def show_lives(lives):
    lives_text = font.render(f"Lives: {lives}", True, white)
    window.blit(lives_text, (width - 100, 10))

def show_game_over():
    game_over_text = font.render("       YOUR GAME  IS END  ", True, red)
    window.blit(game_over_text, (width // 3 - 50, height // 2))

# Main game loop Physics
running = True
while running:
    window.fill(black)
    
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and bullet_state == "ready":
                bullet_pos = player_pos[:]
                bullet_state = "fire"

    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player_pos[0] > 0:
        player_pos[0] -= 10
    if keys[pygame.K_RIGHT] and player_pos[0] < width - player_size:
        player_pos[0] += 10

    if bullet_state == "fire":
        draw_bullet(bullet_pos)
        bullet_pos[1] -= bullet_speed  #bullet speed Management
        if bullet_pos[1] < 0:
            bullet_state = "ready"

    draw_player(player_pos)

    for box in box_pos:
        box[1] += box_speed
        if box[1] > height:
            box[0] = random.randint(0, width - box_size)
            box[1] = 0
            lives -= 1

        if bullet_state == "fire" and box[1] < bullet_pos[1] < box[1] + box_size:
            if box[0] < bullet_pos[0] < box[0] + box_size:
                box[0] = random.randint(0, width - box_size)
                box[1] = 0
                bullet_state = "ready"
                score += 1

        draw_box(box)

    show_score(score)
    show_lives(lives)

    if lives <= 0:
        show_game_over()
        pygame.display.update()
        pygame.time.wait(2000)  # Wait for 2 seconds before quitting
        running = False

    pygame.display.update()
    clock.tick(15)#Reaction Time In The Game

pygame.quit()
