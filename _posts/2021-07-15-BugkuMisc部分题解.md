---
title: BugkuMisc部分题解
categories: CTF
tags: Misc
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

解开flag压缩包后是一张图片，放到010editor里提示crc报错，可以想见是图片的宽高被修改了，crc爆破得到真正的宽高数据(240*270),直接能看到flag

flag{1t_1s_1nterest1n9}









# [蜘蛛侠]

下载下来的压缩包文件有密码，打开压缩包后看到注释

![31.png](https://i.loli.net/2021/07/15/7LUj5VFm9MXTdiZ.png)

将上面的字符放到百度上搜索发现是晋商使用的计数符号，转换为数字后再根据下面的capital number的提示变为汉字的大写数字

key:肆肆壹拾陆玖玖捌拾壹

打开压缩包后hint中说key.jpg被使用python脚本加密了，根据给出的encode.py文件写出解密脚本

```python
import os
data_jpg = open('file.jpg','wb')

def jpg_decode():
    with open("key.jpg", "rb") as handle:
        size = os.path.getsize("key.jpg")
        #print(size)
        i = 0
        while i < size:
            ByteData = handle.read(1)
            process_data = data_decode(ByteData)
            data_write(process_data)
            i = i + 1


def data_decode(bytedata):
    try:
        data = int.from_bytes(bytedata, byteorder='big')
        if data % 2 == 0:
            data = (data ^ 128) + 1
        else:
            data = (data ^ 128) - 1
        data = bytes([data])
        return data
    except ValueError as ve:
        print(ve)


def data_write(process_data):
    data_jpg.write(process_data)

if __name__ == '__main__':
    jpg_decode()
    data_jpg.close()
 ```

解密得到图片

![file.jpg](https://i.loli.net/2021/07/15/vT4AFxoIhK7Sa2U.jpg)

使用010editor打开图片发现在末尾有一串多余的字符

IQ2?kEcY/KK#ojDrHoR'seB

猜测应该是某种编码，最后多次尝试后发现是base92，解密得

password:SilentEye

这就是明示了，使用SilentEye去解密图片，密码就是SilentEye

![32.png](https://i.loli.net/2021/07/15/Mq4F5jSdDOufQYp.png)

flag{spider-man_is_really_cool}









# [图穷匕现]

下载下来就是一张图

![file.jpg](https://i.loli.net/2021/07/15/YoSMZL9ryI6z1Jk.jpg)

根据题目名称能想到应该是在图片文件的尾部藏了东西，打开一看，果然，在尾部有一长串字符，观察形式猜想应该是一串16进制字符，复制下来转换成字符发现是一长串的坐标

根据经验判断应该是要根据坐标绘图，于是我写了个脚本

```python
from PIL import Image
MAX = 271
#二维码大小
with open("file.txt","r",encoding="UTF-8") as f:
    str1 = f.read()
    str1 = str1.split("\n")
pic = Image.new("RGB",(MAX+1, MAX+1))
i=0
for x in range (0,MAX+1):
    for y in range (0,MAX+1):
        m = str(x)
        n = str(y)
        _str = '('+m+','+n+')'        
        if(str1[i] == _str):
            pic.putpixel([x,y],(0, 0, 0))
            i = i+1
        else:
            pic.putpixel([x,y],(255,255,255))
pic.show()
pic.save("1.png")
```

果不其然，导出了一张二维码

![1.png](https://i.loli.net/2021/07/15/iTKSY7kjWbofQaG.png)

扫描二维码后得到flag

flag{40fc0a979f759c8892f4dc045e28b820}









# [听首音乐]

这道题给了一个音频文件，直接使用Audacity打开，发现左声道是一串很明显的摩尔斯电码，将其读出来并转换成字符串

![2.png](https://i.loli.net/2021/07/15/7JrBfIWOuDShHMk.png)

..... -... -.-. ----. ..--- ..... -.... ....- ----. -.-. -... ----- .---- ---.. ---.. ..-. ..... ..--- . -.... .---- --... -.. --... ----- ----. ..--- ----. .---- ----. .---- -.-.

字符串：5BC925649CB0188F52E617D70929191C

flag{5BC925649CB0188F52E617D70929191C}









# [好多数值]

这道题拿到一个txt文件，里面是很多的三个数为一组的数值，而且大部分都是255,255,255。 很自然的想到是rgb数值，那这道题就是要我们将每个点的rgb数值画在图上。

![41.png](https://i.loli.net/2021/07/15/rNSyPei5spnHkx1.png)

开始写脚本

```python
from PIL import Image
with open('1.txt','r',encoding="UTF-8") as f:
    str1 = f.readlines()
w = 503
h = 61366 // w
img = Image.new('RGB', (w, h))

for i in range(61366):
    l = str1[i][:-1].split(',')
    r = int(l[0])
    g = int(l[1])
    b = int(l[2])
    y = i % h
    x = i // h
    img.putpixel((x, y), (r, g, b))
img.show()
img.save("1.png")
```

宽高要根据整体数据数目进行推算，基本思路是分解质因数后一个一个试，先试可能性最大的

拿到图片后直接就是flag

![flag.png](https://i.loli.net/2021/07/15/kl6QSrtHahJN5MF.jpg)









# [convert]

拿到一个txt文件，里面有大量的01二进制码，导入010editor直接当做二进制文本读入，发现是一个rar压缩包，提取出来

解压得到一张图片，图片的注释中有一串base64

ZmxhZ3swMWEyNWVhM2ZkNjM0OWM2ZTYzNWExZDAxOTZlNzVmYn0=

解密得flag{01a25ea3fd6349c6e635a1d0196e75fb}









# [很普通的数独]

这道题给了我25个数独图片，一开始我还试图看看是不是什么图片隐写什么的，但是图片又太多了，而且又没有什么提示。直到我看到了第五张图中的一个细节

![51.png](https://i.loli.net/2021/07/15/lt5rXGodISneTvy.png)

这玩意怎么看着这么像二维码的定位点呢？

然后我发现在第1张图和第21张图还有两个类似的形状，而且25张图中只有三个，正好符合二维码的特征。

这道题就是将二维码用数独图的形式表示了出来，按照有数字的位置一个个涂成黑色拼起来就可以得到结果

![qrcode.jpg](https://i.loli.net/2021/07/15/k15shzSJaZD6GYf.jpg)

扫码得到一串字符

Vm0xd1NtUXlWa1pPVldoVFlUSlNjRlJVVGtOamJGWnlWMjFHVlUxV1ZqTldNakZIWVcxS1IxTnNhRmhoTVZweVdWUkdXbVZHWkhOWGJGcHBWa1paZWxaclpEUmhNVXBYVW14V2FHVnFRVGs9

这是一串base64，多次解码后得

flag{y0ud1any1s1}









# [color]

解压后是7张图片，发现每张图片都有crc报错，通过crc爆破获取正确的宽高之后发现每张图片下方都有一串黑白间隔的色块，如下图所示

![0.png](https://i.loli.net/2021/07/15/1BqoIwP8xKzET2W.png)

当时就想到分别代表01,转化为二进制数。

然后就尬住了，不知道下一步该怎么做，尝试了很多种方式都没有成功

直到我将6串01分行放在一起，突然意识到竖着看的话是7行，如果将第一位看做0的话正好是二进制表示字符的位数

![0101.jpg](https://i.loli.net/2021/07/15/WmI4rV8ijfh5Nyq.jpg)

当即看了一下第一列，发现是字符"f"，这不就是flag的起始字符吗，依次转化，得到flag

flag{Png1n7erEs7iof}









# [怀疑人生]

下载下来是3个文件，分别是ctf1.zip,ctf2.jpg,ctf3.jpg

先看ctf3.jpg，这个看起来像一个二维码，直接扫描

![ctf3.jpg](https://i.loli.net/2021/07/15/nqGbuW3lHoysUpA.png)

得到内容为：12580}

这应该是flag的最后一部分，那就是flag分三部分，一个文件里一部分

再看ctf2，在图片文件尾部发现了一个压缩包，分解出来并解压看到如下图所示

![ctf2.png](https://i.loli.net/2021/07/15/3vEcQWwp4PkxeYC.png)

搜索之后发现是ook！的一种变种，解密得:3oD54e

最后看ctf1.zip,没有什么线索，也试过了不是伪加密，暴力破解也没有得到解压密码，在这里卡了很久没有方法，我就直接挂着爆破去忙别的事情，忙完回来发现爆破有了解，密码就是password

（逐渐理解了题目名字叫怀疑人生的原因）

里面是一串base64：XHU2Nlx1NmNcdTYxXHU2N1x1N2JcdTY4XHU2MVx1NjNcdTZiXHU2NVx1NzI=

解密得：\u66\u6c\u61\u67\u7b\u68\u61\u63\u6b\u65\u72

很明显是16进制的字符，转成10进制并用ascii编码转换成字符得：
flag{hccker

把三段flag拼起来得到了flag

flag{hccker3oD54e12580}
