import pygame
from pygame.locals import*
import random

pygame.init()

clock=pygame.time.Clock()
fps=30
screen_height=700
screen_width=1040
player_pos=[400,350]
speed_x=0
speed_y=0
enemy_one_pos=[random.randint(0,screen_width-110),0]
enemy_one_speed=0
enemy_two_pos=[random.randint(0,screen_width-110),0]
enemy_two_speed=0
score=0
level=1
best_score=0

screen=pygame.display.set_mode((screen_width,screen_height))

land=pygame.image.load('images.jfif')
bg=pygame.transform.scale(land,(screen_width,screen_height))

char=pygame.image.load('bm.jpg')
player=pygame.transform.scale(char,(110,160))

char2=pygame.image.load('bn.jpg')
enemy=pygame.transform.scale(char2,(110,110))

def cheak_best_score():
    global best_score
    file=open('best.txt','r')
    best_score=int(file.read())
    file.close()
    if best_score<score:
        best_score=score
        file=open('best.txt','w')
        file.write(str(best_score))
        file.close()

def gameover():
    cheak_best_score()
    font=pygame.font.Font(None,100)
    gameover_text=font.render('gameover!',True,(255,0,0))
    screen.blit(gameover_text,(200,200))
    font_2=pygame.font.Font(None,40)
    score_text=font_2.render('score:'+str(score),True,(255,255,0))
    screen.blit(score_text,(300,300))
    best_score_text=font_2.render('Best score:'+str(best_score),True,(255,255,0))
    screen.blit(best_score_text,(300,350))

    pygame.display.update()
    pygame.time.delay(5000)

def update_information():
    font=pygame.font.Font(None,40)
    score_text=font.render('score:'+str(score),True,(255,255,0))
    screen.blit(score_text,(20,20))
    level_text=font.render('Level:'+str(level),True,(255,255,0))
    screen.blit(level_text,(screen_width-130,20))

def move_player():
    player_pos[0]+=speed_x
    player_pos[1]+=speed_y

    if player_pos[0]<=0:
        player_pos[0]=0
    if player_pos[0]>=screen_width-110:
        player_pos[0]=screen_width-110
    
    if player_pos[1]<=0:
        player_pos[1]=0
    if player_pos[1]>=screen_height-130:
        player_pos[1]=screen_height-130
    
    screen.blit(player,(player_pos[0],player_pos[1]))

def move_enemy():
    global score
    enemy_one_pos[1]+=enemy_one_speed
    enemy_two_pos[1]+=enemy_two_speed

    if 0<=enemy_one_pos[1] and enemy_one_pos[1]<=screen_height:
        enemy_one_pos[1]+=enemy_one_speed
    else:
        enemy_one_pos[1]=0
        enemy_one_pos[0]=random.randint(0,screen_width-110)
        score+=1

    if 0<=enemy_two_pos[1] and enemy_two_pos[1]<=screen_height:
        enemy_two_pos[1]+=enemy_two_speed
    else:
        enemy_two_pos[1]=0
        enemy_two_pos[0]=random.randint(0,screen_width-110)
        score+=1


    screen.blit(enemy,(enemy_one_pos[0], enemy_one_pos[1]))
    screen.blit(enemy,(enemy_two_pos[0],enemy_two_pos[1]))

def cheak_collison(p,e):
    p_x=p[0]
    p_y=p[1]
    e_x=e[0]
    e_y=e[1]

    if(p_x<e_x<(p_x+110))or (p_x<=(e_x+110)<(p_x+110)):
        if(p_y<=e_y<(p_y+160))or(p_y<(e_y+120)<(p_y+160)):
            return 1
    else:
        return 2

def show_start_countdown():
    font=pygame.font.Font(None,400)
    for i in 3,2,1:
        i=str(i)
        countdown_text=font.render(i,True,(255,0,0))
        screen.blit(countdown_text,(150,300))
        pygame.display.update()
        screen.fill((0,0,0))
        pygame.time.delay(1000)
    countdown_text=font.render('Go',True,(0,255,0))
    screen.blit(countdown_text,(150,300))
    pygame.display.update()
    screen.fill((0,0,0))
    pygame.time.delay(1000)

pygame.mixer.music.load('_- Instrumental 7 (320).mp3')
pygame.mixer.music.play(-1)

show_start_countdown()

running=True
while running:
    
    screen.blit(bg,(0,0))

    level=1+score//10

    enemy_one_speed=random.randint(level,level+4)
    enemy_two_speed=random.randint(level,level+4)

    for event in pygame.event.get():
        if event.type==QUIT:
            quit()
            running=False
        if event.type==KEYDOWN:
            if event.key==K_RIGHT:
                speed_x=8
                speed_y=0
            if event.key==K_LEFT:
                speed_x=-8
                speed_y=0
            if event.key==K_UP:
                speed_x=0
                speed_y=-8
            if event.key==K_DOWN:
                speed_x=0
                speed_y=8

    if cheak_collison(player_pos,enemy_one_pos)==1:
        pygame.mixer.music.load('Game-Over-Sound-Effect-3.mp3')
        pygame.mixer.music.play()
        gameover()
        running=False
        break
    if cheak_collison(player_pos,enemy_two_pos)==1:
        pygame.mixer.music.load('Game-Over-Sound-Effect-3.mp3')
        pygame.mixer.music.play()
        gameover()
        running=False
        break
    
    move_player()
    move_enemy()
    update_information()
    clock.tick(fps)
    pygame.display.update()
