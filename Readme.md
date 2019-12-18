### **本游戏基于python的第三方模块pygame开发**

##### 游戏运行环境:

- python   v3.6(或大于3.6)

- pygame  v1.9.6
- 平台 Linux,Windows



**游戏玩法**:

- 游戏分为两个模式：禅宗模式和经典模式，分别对应芒果和西瓜，切开它们开始游戏!
- 游戏中操作鼠标光标划过水果即可切开水果(鼠标不需要点击)；
- 经典模式下miss掉3个水果游戏结束;
- 禅宗模式下无限切水果。
- 主页中划过炸弹退出游戏







**游戏机制与实现**:

游戏中的水果分为两种状态：

- 1.被抛出时做斜上抛运动, 代码实现如下:

```python
def __init__(self, window, image_path, speed, turn_angel, flag):
        pygame.sprite.Sprite.__init__(self)

        # 游戏窗口
        self.window = window

        # 导入水果图像并获取其矩形区域
        self.image = pygame.image.load(image_path)
        self.rect = self.image.get_rect()

        # 水果抛出时x坐标取随机数
        self.rect.x = random.randint(0, Manager.WIDTH)

        # 水果初始y坐标
        self.rect.y = Manager.HEIGHT

        # 抛出时速度
        self.speed = speed

        # 旋转速度
        self.turn_angel = turn_angel

        # 水果抛出时与窗口下水平线的夹角弧度，因为要用到随机函数, 所以取整数， 使用时除以100
        self.throw_angel = 157

        # 水果抛出后所经历的时间, 初始化为0
        self.fruit_t = 0
        
def update(self):
        """ 水果运动状态更新 """

        # 如果水果的初始X坐标位于窗口左边区域
        # 取抛出时弧度在1.4  ~ 1.57(为了方便运算，传入参数时需要将弧度乘以100) 之间(70度至90度之间)
        if self.rect.x <= Manager.WIDTH / 2:
            self.throw_angel = random.randint(140, 157)

        # 如果水果的初始X坐标位于窗口右侧区域
        # 取抛出时弧度在1.57 * 100 ~ 1.75 * 100之间(90度至110度之间)
        elif self.rect.x >= Manager.WIDTH / 2:
            self.throw_angel = random.randint(157, 175)

        # 水果旋转后的新图像
        new_fruit = pygame.transform.rotate(self.image, self.v_angel)

        # 将旋转后的新图像贴入游戏窗口, 
        # 注意, 旋转后的图像尺寸以及像素都不一样了(尺寸变大了), 所以坐标需要进行适当处理
        self.window.blit(new_fruit, (self.rect.x + self.rect.width / 2 - new_fruit.get_width() / 2,
                                     self.rect.y + self.rect.height / 2 - new_fruit.get_height() / 2))

        # 水果抛出后的运动时水平匀速运动以及竖直向上的变速运动到达最高点时下落, 
        # 所以可以判断水果做的是斜上抛运动
        # 利用重力加速度来求出每隔一段时间水果运动后的y坐标(重力加速度已添加
        # 到全局变量中，此处关注计算公式即可)
        # 公式: v0 * t * sin(α) - g * t^2 / 2
        if self.rect.y >= Manager.HEIGHT + self.rect.height:
            if self.flag != 5:
                Manager.classic_miss += 1
            self.kill()
        self.rect.y -= self.v0 * self.fruit_t * math.sin(self.throw_angel / 100) - (Manager.G *
                                                                                    self.fruit_t ** 2 / 10) / 2

```



- 水果到达最高点后如果被切开，运动状态将转为平抛运动, 如下:

- ```python
   # 水果被切开之后的切片做的是平抛运动
          # 用重力加速度来求出每隔一段时间水果运动后的y坐标
          # 公式: h += v0 * t * sin(α) - g * t^2 / 2
          if self.rect.y >= Manager.HEIGHT:
              self.kill()
          self.rect.y += Manager.G * self.fruit_t ** 2 / 2
    
          # 计算水果在水平方向的位移之后的X坐标, 匀速运动
          # 公式: v0 * t * cos(α)
          self.rect.x += self.v0 * self.fruit_t
    
          # 累加经过的时间
          self.fruit_t += 0.01
    
          # 累加旋转总角度
          self.v_angel += self.turn_angel
    
  ```

  



### 游戏截图

**游戏主页**

![](.\img\游戏主页.png)



**经典模式**

![](.\img\经典模式.png)



**禅宗模式**

![](.\img\禅宗模式.png)