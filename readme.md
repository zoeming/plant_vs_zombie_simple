源github地址：[https://github.com/llx330441824/plant_vs_zombie_simple](https://github.com/llx330441824/plant_vs_zombie_simple)

游戏内容：
左边的滚轮机会自动生成植物的卡牌，鼠标单击可以选中植物， 被选中的植物将跟随鼠标移动，再次点击后，可在草地中放置植物。
豌豆射手可以发射豌豆，寒冰射手发射的冰冻豌豆可以使僵尸减速，三头豌豆射手可以发射三颗豌豆，坚果可以阻止僵尸前进，吹风草可以将所有的僵尸吹到屏幕外，地刺会持续对僵尸进行攻击。
若对放置的植物不满意，可以使用铲子，将草地上的植物移除。不同的僵尸血量不同，移动速度不同。击杀棒球僵尸后将获得随机奖励，奖励类型有：全屏僵尸死亡，全屏僵尸静止两秒等。当有僵尸闯入房间，游戏结束。点击图标可重新开始游戏。


分为三大类类对象：子弹、植物和僵尸

### 1. 植物

#### 1.1 植物属性

植物的状态有：WAIT(0),STOP(1),MOVE(2),LIFE(3),DEAD(4)

植物的血量：live，每被僵尸攻击一次血量减1

植物等待时的位置，y-=ySpeed

##### 豌豆射手

live = 3

##### 寒冰射手(SnowPea)

live = 5

攻击时僵尸减速xSpeed=1

##### 三头豌豆射手(ThreePeater)

live = 7

##### 坚果(WallNut)

live = 10

##### 吹风草(Blover)

live = 3

攻击时僵尸持续减速直至被吹走xSpeed -= 2

##### 地刺(Spikerock)

live = 5

#### 1.2 生成植物

随机生成

```java
Random rand = new Random();
		int type = rand.nextInt(30);
		// 控制植物的出场概率
		if(type<5) {
			return new Repeater();
		}else if(type<10) {
			return new SnowPea();
		}else if(type<15) {
			return new ThreePeater();
		}else if(type<20) {
			return new Spikerock();
		}else if(type<25) {
			return new Blover();
		}else {
			return new WallNut();
		}
```

生成的植物先放在滚轮机集合中，植物出现频率：plantTime%300==0



### 2. 子弹

#### 2.1 子弹的属性

子弹的状态有：LIFE(0),DEAD(1)

子弹的属性：x, y, width, height, xSpeed

##### 冰冻子弹(SnowBullet)

僵尸 live-1; xSpeed-1

##### 豌豆子弹(PeaBullter)

僵尸 live-1



### 3. 僵尸

#### 3.1 僵尸属性

僵尸的状态有：LIFE(0), ATTACK(1), DEAD(2)

子弹的属性：x, y, width, height, xSpeed

##### 铁桶僵尸(Zombie0)

live = 20

xSpeed = 2

##### 安全帽僵尸(Zombie1)

live = 15

xSPeed = 3

##### 旗子僵尸(Zombie2)

live = 15

xSPeed = 3

##### 棒球僵尸(Zombie3)

live = 25

xSPeed = 4

#### 3.2 生成僵尸

用随机数随机生成僵尸

```Java
Random rand = new Random();
		// 控制不同种类僵尸出现的概率
		int type = rand.nextInt(20);
		if(type<5) {
			return new Zombie0();
		}else if(type<10) {
			return new Zombie1();
		}else if(type<15) {
			return new Zombie2();
		}else {
			return new Zombie3();
		}
```

#### 3.3 僵尸攻击

攻击间隔：zombieHitTime++%100==0

攻击范围中植物live-1

#### 3.4 僵尸死亡

僵尸live<0，僵尸死亡

如果棒球僵尸死亡或所有僵尸死亡，有额外奖励



### 4. 游戏流程

#### 4.1 构建对象

- 僵尸集合(zomies)

	僵尸集合中只保存活着的僵尸

- 植物集合

	- 滚轮机上的植物(plants)
		- stop
		- wait
	- 草地上的植物(plantsLife)
		- life
		- move

- 子弹集合(bullets)

	子弹集合中只保存还在页面上的子弹（子弹攻击到僵尸会消失）

- 草地集合(glasses)

	- EMPTY
	- HOLD

	草地背景可以分为 9*5，其中每块草地WIDTH=80, HEIGHT=100

	草地集合记录每块草地(记录左上角坐标)

	检测草地上植物的位置和草地的位置，如果重合则表示该块草地上有植物

- 铲子集合(shovels)

	保证页面上只有一把铲子

#### 4.2 游戏结束

僵尸跑进了房子，游戏结束

游戏结束，所有对象集合清零