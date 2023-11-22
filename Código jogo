# pygame-Flappy
código flappy bird (pygame)

import pygame
import random
import sys

# Inicializa o Pygame e o mixer
pygame.init()
pygame.mixer.init()

# Definição das constantes
SCREEN_WIDTH, SCREEN_HEIGHT = 400, 600
GRAVITY = 0.4
BIRD_JUMP = 7
PIPE_WIDTH = 70
PIPE_GAP = 200
PIPE_SPEED = 4
PIPE_FREQUENCY = 1500
BACKGROUND_SPEED = 0.5
GROUND_HEIGHT = 70

# Configurações da tela
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Flappy Bird")

# Carrega as imagens
bird_body_img = pygame.image.load(
    'C:/Users/gabri/pythonProject/FlappyBird/imagens flappy/passarinho.png').convert_alpha()
bird_body_img = pygame.transform.scale(bird_body_img, (30, 30))
bird_wing_up = pygame.image.load('C:/Users/gabri/pythonProject/FlappyBird/imagens flappy/asa cima.png').convert_alpha()
bird_wing_up = pygame.transform.scale(bird_wing_up, (20, 20))
bird_wing_down = pygame.image.load(
    'C:/Users/gabri/PythonProject/FlappyBird/imagens flappy/asa baixo.png').convert_alpha()
bird_wing_down = pygame.transform.scale(bird_wing_down, (20, 20))
bird_frames = [bird_wing_up, bird_wing_down]
bird_index = 0
background_img = pygame.image.load(
    'C:/Users/gabri/pythonProject/FlappyBird/imagens flappy/fundo flappy bird.png').convert_alpha()
ground_img = pygame.image.load('C:/Users/gabri/PythonProject/FlappyBird/imagens flappy/chão flappy.png').convert_alpha()
ground_img = pygame.transform.scale(ground_img, (SCREEN_WIDTH, GROUND_HEIGHT))
pipe_img = pygame.image.load('C:/Users/gabri/PythonProject/FlappyBird/imagens flappy/cano.png').convert_alpha()
pipe_img = pygame.transform.scale(pipe_img, (PIPE_WIDTH, SCREEN_HEIGHT))
play_button_image = pygame.image.load(
    'C:/Users/gabri/PythonProject/FlappyBird/imagens flappy/botão de play.png').convert_alpha()
play_button_image = pygame.transform.scale(play_button_image, (100, 50))
play_button_rect = play_button_image.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2 + 50))

# Carrega os sons
jump_sound = pygame.mixer.Sound('C:/Users/gabri/PythonProject/FlappyBird/sons flappy/sfx_wing.wav')
score_sound = pygame.mixer.Sound('C:/Users/gabri/PythonProject/FlappyBird/sons flappy/sfx_point.wav')
death_sound = pygame.mixer.Sound('C:/Users/gabri/PythonProject/FlappyBird/sons flappy/sfx_die.wav')
pygame.mixer.music.load('C:/Users/gabri/PythonProject/FlappyBird/sons flappy/Flappy Bird Remastered - Soundtrack.mp3')
pygame.mixer.music.play(-1)

# Variáveis do jogo
font = pygame.font.Font(None, 36)
clock = pygame.time.Clock()
score = 0
high_score = 0
pipes = []
background_x = 0
ground_x = 0

game_just_ended = False

# Funções do jogo
def draw_ground(ground_x):
    screen.blit(ground_img, (ground_x, SCREEN_HEIGHT - GROUND_HEIGHT))
    screen.blit(ground_img, (ground_x + SCREEN_WIDTH, SCREEN_HEIGHT - GROUND_HEIGHT))
    return ground_x


def draw_background(background_x):
    rel_x = background_x % background_img.get_rect().width
    screen.blit(background_img, (rel_x - background_img.get_rect().width, 0))
    if rel_x < SCREEN_WIDTH:
        screen.blit(background_img, (rel_x, 0))
    background_x -= BACKGROUND_SPEED
    return background_x


def add_pipe(pipes):
    gap_y = random.randint(100, SCREEN_HEIGHT - 100 - PIPE_GAP)  # Posição Y do topo do gap
    top_pipe_height = gap_y
    bottom_pipe_height = SCREEN_HEIGHT - gap_y - PIPE_GAP
    top_pipe = pygame.Rect(SCREEN_WIDTH, 0, PIPE_WIDTH, top_pipe_height)
    bottom_pipe = pygame.Rect(SCREEN_WIDTH, gap_y + PIPE_GAP, PIPE_WIDTH, bottom_pipe_height)
    pipes.append([top_pipe, bottom_pipe, False])  # Usa lista em vez de tuplo


def move_pipes(pipes):
    for pipe_pair in pipes:
        pipe_pair[0].x -= PIPE_SPEED
        pipe_pair[1].x -= PIPE_SPEED
    pipes = [pipe_pair for pipe_pair in pipes if pipe_pair[0].x > -PIPE_WIDTH]
    return pipes


def draw_pipes(pipes):
    for pipe in pipes:
        # Redimensiona e inverte o cano superior
        top_pipe_img = pygame.transform.scale(pipe_img, (PIPE_WIDTH, pipe[0].height))
        top_pipe_img = pygame.transform.flip(top_pipe_img, False, True)
        screen.blit(top_pipe_img, pipe[0].topleft)

        # Redimensiona o cano inferior
        bottom_pipe_img = pygame.transform.scale(pipe_img, (PIPE_WIDTH, pipe[1].height))
        screen.blit(bottom_pipe_img, pipe[1].topleft)


def check_collisions(pipes, bird_rect):
    for pipe_pair in pipes:
        if bird_rect.colliderect(pipe_pair[0]) or bird_rect.colliderect(pipe_pair[1]):
            death_sound.play()
            return True

    # Verifica se o pássaro atingiu o topo da tela
    if bird_rect.top <= 0:
        death_sound.play()
        return True

    # Ajusta a verificação de colisão do chão para ser mais próxima do visual
    ground_collision_buffer = 37  # Você pode ajustar esse valor conforme necessário
    if bird_rect.bottom >= SCREEN_HEIGHT - GROUND_HEIGHT + ground_collision_buffer:
        death_sound.play()
        return True

    return False

def main_game():
    global score, high_score, pipes, background_x, ground_x, game_just_ended
    bird_pos = [50, SCREEN_HEIGHT // 2]
    bird_vel = 0
    score = 0
    pipes = []
    background_x = 0
    running = True
    last_pipe = pygame.time.get_ticks()
    passed_pipe = False

    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    bird_vel = -BIRD_JUMP
                    jump_sound.play()

        background_x = draw_background(background_x)

        time_now = pygame.time.get_ticks()
        if time_now - last_pipe > PIPE_FREQUENCY:
            last_pipe = time_now
            add_pipe(pipes)
            passed_pipe = False  # Restaura para verificar a passagem pelo novo cano

        bird_vel += GRAVITY
        bird_pos[1] += bird_vel
        bird_rect = bird_body_img.get_rect(center=bird_pos)

        pipes = move_pipes(pipes)
        draw_pipes(pipes)

        ground_x -= BACKGROUND_SPEED
        if ground_x <= -SCREEN_WIDTH:
            ground_x = 0
        ground_x = draw_ground(ground_x)  # Desenha o chão que se move

        # Verifica se o pássaro passou pelos canos para incrementar a pontuação
        for pipe_pair in pipes:
            if pipe_pair[0].right < bird_pos[0] and not passed_pipe:
                score += 1
                score_sound.play()
                passed_pipe = True  # Evita que a pontuação seja incrementada mais de uma vez por cano

        if check_collisions(pipes, bird_rect):
            running = False
            if score > high_score:
                high_score = score

        screen.blit(bird_body_img, bird_rect)
        score_text = font.render(f'Score: {score}', True, (255, 255, 255))
        screen.blit(score_text, (SCREEN_WIDTH // 2 - score_text.get_width() // 2, 20))

        pygame.display.update()
        clock.tick(80)

        if not running:
            pygame.time.delay(1000)
            game_just_ended = True
            show_menu()

def show_menu():
    global score, game_just_ended
    menu_running = True
    background_x = 0
    bird_animation_timer = pygame.time.get_ticks()
    bird_frame_index = 0
    bird_center_pos = (SCREEN_WIDTH // 2, SCREEN_HEIGHT // 3)

    if game_just_ended:
        pygame.time.wait(500)  # Espera 500 milissegundos
        game_just_ended = False

    # Reseta a pontuação cada vez que o menu é mostrado
    score = 0
    # Limpa a fila de eventos para remover cliques pendentes antes de mostrar o menu
    pygame.event.clear()
    # Adiciona um pequeno atraso antes de processar eventos
    pygame.time.wait(300)  # Espera 300 milissegundos

    # Define a fonte para o título
    title_font = pygame.font.Font('C:/Users/gabri/pythonProject/FlappyBird/fonte/FlappybirdyRegular-KaBW.ttf', 60)
    title_text = title_font.render('Passarinho Pula pula', True, (255, 255, 255))
    title_pos = title_text.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 6))

    # Define a fonte para os créditos
    credit_font = pygame.font.Font('C:/Users/gabri/pythonProject/FlappyBird/fonte/FlappyBirdRegular-9Pq0.ttf',
                                   27)  # Fonte menor para os créditos
    credit_text = credit_font.render('by:Gabriel Santana/GPT', True, (23, 23, 23))  # O texto dos créditos
    credit_pos = credit_text.get_rect(bottomright=(SCREEN_WIDTH - 10, SCREEN_HEIGHT - 10))

    global ground_x  # Adiciona esta linha para usar a variável global ground_x
    ground_x = draw_ground(ground_x)

    while menu_running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.MOUSEBUTTONDOWN:
                if play_button_rect.collidepoint(pygame.mouse.get_pos()):
                    menu_running = False

        background_x = draw_background(background_x)

        # Desenha o título
        screen.blit(title_text, title_pos)

        # Desenha o crédito no canto inferior direito
        screen.blit(credit_text, credit_pos)

        # Animação do pássaro
        if pygame.time.get_ticks() - bird_animation_timer > 200:
            bird_frame_index = (bird_frame_index + 1) % len(bird_frames)
            bird_animation_timer = pygame.time.get_ticks()

        # Desenha o corpo do pássaro
        bird_body_rect = bird_body_img.get_rect(center=bird_center_pos)
        screen.blit(bird_body_img, bird_body_rect)

        # Ajuste a posição da asa
        wing_offset_x = -9  # Ajuste conforme necessário
        wing_offset_y = -1  # Ajuste conforme necessário

        # Desenha a asa do pássaro em cima do corpo
        bird_wing_rect = bird_frames[bird_frame_index].get_rect(center=bird_body_rect.center)
        bird_wing_rect.x += wing_offset_x
        bird_wing_rect.y += wing_offset_y
        screen.blit(bird_frames[bird_frame_index], bird_wing_rect)

        screen.blit(play_button_image, play_button_rect)
        pygame.display.update()
        clock.tick(60)

def run_game():
    def run_game():
        global score, high_score, pipes, background_x, ground_x
        in_menu = True

        while True:
            if in_menu:
                show_menu()
                in_menu = False
                reset_game_state()  # Reinicia o estado do jogo
            else:
                main_game()
                in_menu = True
                pygame.event.clear()  # Limpa os eventos após o término do jogo


def reset_game_state():
    global score, pipes, background_x, ground_x
    score = 0
    pipes = []
    background_x = 0
    ground_x = 0

while True:
    show_menu()
    main_game()
    run_game()
