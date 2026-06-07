# ping-pong
import pygame

class GameSprite():
    def __init__(self, x, y, width, height, color=None):
        self.rect = pygame.Rect(x, y, width, height)
        self.color = color
    def draw(self, surface):
        pygame.draw.rect(surface, self.color, self.rect)
    def collidepoint(self, x, y):
        return self.rect.collidepoint(x, y)
    def colliderect(self, rect):
        return self.rect.colliderect(rect)

class Player(GameSprite):
    def __init__(self, x, y, width, height, color=None):
        super().__init__(x, y, width, height, color)
        self.speed = 5
    def update(self):
        keys_pressed = pygame.key.get_pressed()
        if keys_pressed[pygame.K_w] and self.rect.y > 0:
            self.rect.y -= self.speed
        if keys_pressed[pygame.K_s] and self.rect.y < window_height - self.rect.height:
            self.rect.y += self.speed

class Player2(GameSprite):
    def __init__(self, x, y, width, height, color=None):
        super().__init__(x, y, width, height, color)
        self.speed = 15
    def update(self):
        keys_pressed = pygame.key.get_pressed()
        if keys_pressed[pygame.K_UP] and self.rect.y > 0:
            self.rect.y -= self.speed
        if keys_pressed[pygame.K_DOWN] and self.rect.y < window_height - self.rect.height:
            self.rect.y += self.speed

class Ball(GameSprite):
    def __init__(self, x, y, width, height, speed, color=None, image_path=None):
        super().__init__(x, y, width, height, color)
        self.dx = speed
        self.dy = speed
        self.no_lose = False
        self.image = None
        if image_path:
            self.image = pygame.image.load(image_path)
            self.image = pygame.transform.scale(self.image, (width, height))

    def draw(self, surface):
        if self.image is not None:
            surface.blit(self.image, self.rect)
        else:
            pygame.draw.rect(surface, self.color, self.rect)

    def update(self, players):
        self.rect.x += self.dx
        self.rect.y += self.dy
        for player in players:
            if self.colliderect(player.rect):
                offset = (self.rect.centery - player.rect.centery) / (player.rect.height / 2)
                self.dy = offset * 5
                self.dx *= -1
                break 
        if self.rect.top <= 0 or self.rect.bottom >= window_height:
            self.dy *= -1
        if not self.no_lose:
            if self.rect.left <= 0 or self.rect.right >= window_width:
                self.dx *= -1

pygame.init()

window_width = 500
window_height = 430
window = pygame.display.set_mode((window_width, window_height))
clock = pygame.time.Clock()
game = True

player1 = Player(10, 100, 15, 80, (50, 205, 50))
player2 = Player2(window_width - 25, 100, 15, 80, (50, 205, 50))
players = [player1, player2]

ball = Ball(window_width//2 - 10, window_height//2 - 10, 20, 20, 5, (255, 0, 0))

while game:
    window.fill((0, 0, 0))
    for e in pygame.event.get():
        if e.type == pygame.QUIT:
            game = False

    player1.update()
    player2.update()
    ball.update(players)

    player1.draw(window)
    player2.draw(window)
    ball.draw(window)

    pygame.display.update()
    clock.tick(60)
