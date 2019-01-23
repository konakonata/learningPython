1.寻找素材，各种图片集合

```python
#coding=utf-8
#1.使用框架====================
import pygame #导入框架包
from pygame.locals import *
#用面向对象的思想
class HeroPlane(object):
    def __init__(self):
        self.x=230
        self.y=890
        self.screen = screen
#如果此脚本是直接用来执行的，就要用这个if
if __name__ == '__main__':
    #设置窗体，宽×高
    screen = pygame.display.set_mode((480, 890), 0, 32)
    #设置背景图片
    bgImageFile = './feiji/background.png' #图片路径
    #转换背景图片
    backgroud = pygame.image.load(bgImageFile).convert()
   	#3.创建玩家的飞机=============
    hero = pygame.image.load("filepath").convert()
    #如果不加循环，窗口显示之后会立刻关闭
    while True：
    	#把图片放到窗口中显示
    	screen.blit(background,(0,0))#设置放置的坐标点
        #把飞机放入窗口
        screen.blit(hero,(0,0))#设置起始坐标点
        #2.检测键盘========================
        for event in pygame.envent.get():
            #点击事件 是关闭
            if event.type == QUIT:
                print("exit")
                exit()
            #点击事件是键盘
            elif event.type == KEYDOWN:
                #左
                if event.key == K_a or event.key == K_LEFT:
                    print("left")
                    #控制飞机的移动
                #右
                elif event.key == K_d or event.key == K_RIGHT:
                    print("right")
                #发射子弹
                elif event.key == K_SPACE:
               		print("space")
                
        #更新界面
        pygame.display.update()
```

