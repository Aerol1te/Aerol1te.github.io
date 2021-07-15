---
title: BugkuMisc部分题解
categories: Misc
date: 2021-7-15 9:10:17
---
# [Poker Game]

这道题刚下载下来只给了两个图片，一个有关flag格式的提示以及一个带密码的压缩包。肯定是首先看图片，图片如下

![1.jpg](https://i.loli.net/2021/07/15/8hExinaXO5R9mUo.jpg)

把图片拖到010editor里面，可以发现大王里藏了一个压缩包，小王里藏了一个png图片文件，这里我使用foremost分离，之后打开png文件发现是一个二维码的一半

![2.png](https://i.loli.net/2021/07/15/6uabHUzZrFCGY2s.png)

毫无疑问另一半肯定是在大王图片分离出的压缩包里，尝试打开压缩包，这个压缩包显示是加密的，而且没有什么明显提示，首先进行爆破，发现并不是简单的数字密码，再换别的思路，使用010editor打开后发现全局加密位和数据加密位都是0900按常理说应该不是伪加密了，但我尝试修改了一下，发现可以打开了，确实是伪加密。

![3.png](https://i.loli.net/2021/07/15/udROzyl82vFCpH3.png)

上图所示的字符串应该很明显了，就是图片用base64加密之后的格式，直接base64转图片得到二维码的另一半，把两张二维码拼在一起。这里还有一个坑，图片上只有右上角一个定位点，二维码想要正确识别需要有三个定位点，手动抠图复制粘贴把图片还原完全后用cqr扫描，得到压缩包密码。

![4.png](https://i.loli.net/2021/07/15/zbAmQGhWkC93uSL.png)

key{P0ke_Paper}

poke压缩包内有很多图片，是扑克牌3-A，但是K.jpg文件无法读取，查看后发现K.jpg实际上是一个zip文件，修改后缀，在压缩包内看到了一个K.jpg，但是这个压缩包有密码。查看David's words.txt文件，里面给出了如下图所示的东西

![5.jpg](https://i.loli.net/2021/07/15/NqRp2A7r98BVYeP.png)

"Only A is 1"应该是最重要的提示，再根据下面这一长串没意义的字符，想到应该是要改成0,1，且A是1。一开始我想的是每个数字代表一个0,1。但在我转换后发现无法识别，而且格式非常不对。于是我将所有的其他字符统一表示0，A表示1，转换为字符后如下图所示，是一个base64字符串

![6.jpg](https://i.loli.net/2021/07/15/1bCWwQ5XuaZUReD.png)

解密后是Happy to tell you key is Key{OMG_Youdoit}
用这个key解压k.zip，得到图片和提示

![K.jpg](https://i.loli.net/2021/07/15/9qznaPwkJGOSeop.jpg)

图片是半张图片，提示中说这是一个"Reverse flag",而且说了要修复这张图片，使用010editor打开，根据半张图片的形式，想到可能是要修改高度，将高度从550改成1100，出现了整张图片。将图片放到stegsolve里看一下，改变通道后在图片底部发现了奇怪的东西

![7.png](https://i.loli.net/2021/07/15/in3465LHwrDjYVp.png)

再根据之前的提示，将图片倒过来就能看出flag

flag{Poker_F@ce}

PS：这个flag是真的考验眼力，好难看清

# [只有黑棋的棋盘]

给的png图片尾部藏了一个zip文件，分离并解压后得到了一个passwd.txt

![passwd.png](https://i.loli.net/2021/07/15/Blw5Qm2SXTPW41L.png)

![只有黑棋的棋盘.png](https://i.loli.net/2021/07/15/xksmpXSfOenubgq.png)

联系到图片本身，可以想到应该是根据棋子位置得出一串字符，按照passwd.txt里的格式得到字符串（这里要注意棋盘上的字母并不全，只能作为坐标参考，我一开始按照上面的数结果被坑了）

字符串我这里不贴了，自己去看！

解开flag压缩包后是一张图片，放到010editor里提示crc报错，可以想见是图片的宽高被修改了，crc爆破得到真正的宽高数据(240*270)
直接能看到flag
flag{1t_1s_1nterest1n9}

