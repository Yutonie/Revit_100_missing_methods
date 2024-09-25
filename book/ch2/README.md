# python threading

## 介紹

:::success
在 Python 中若要撰寫多執行緒（multithreading）的平行化程式，最基本的方式是使用 threading 這個模組來建立子執行緒。[參考](https://blog.gtwang.org/programming/python-threading-multithreaded-programming-tutorial/)
:::


## 教學參考

python官方文件:(中文翻譯會有大陸、台灣用詞落差)
https://docs.python.org/3.8/library/threading.html
中文範例:
https://steam.oxxostudio.tw/category/python/library/threading.html
進程、線程、協程:
https://ithelp.ithome.com.tw/articles/10242047
用Queue模組
https://docs.python.org/zh-tw/3/library/queue.html

## 模組功能

* threading.Thread()
    - start() 開始執行程序
    - join() 等待程序結束
    
* Lock(鎖定)
    - 當Thread執行程序時用了lock.require
* Event(事件)



## 語法

```python
import threading    #調用threading package

def Hi():
    print('nothing')

AA = threading.Thread(target=Hi)    #AA為多執行緒，並呼叫Hi涵式
AA.start()     #開始
AA.join()    #等待完成

lock = threading.Lock()    #建立鎖定
lock.acquire()  #獲得鎖定
lock.release()  #釋放鎖定

event = threading.Event()   #註冊事件
event.wait() #等待事件觸發
event.set() #觸發事件
event.clear()   #觸發後還原事件
```


## 範例

**範例一:** 同時執行aa()和bb()函式
```
import threading
import time

def aa():
    i = 0
    while i<5:
        i = i + 1
        time.sleep(0.5)
        print('A:', i)

def bb():
    i = 0
    while i<50:
        i = i + 10
        time.sleep(0.5)
        print('B:', i)

a = threading.Thread(target=aa)  # 建立新的執行緒
b = threading.Thread(target=bb)  # 建立新的執行緒

a.start()  # 啟用執行緒
b.start()  # 啟用執行緒

'''輸出結果
A: 1
B: 10
A: 2
B: 20
A: 3
B: 30
A: 4
B: 40
A: 5
'''
```

----

**範例二:** join()等待用法
```
import threading
import time

def aa():
    i = 0
    while i<5:
        i = i + 1
        time.sleep(0.5)
        print('A:', i)

def bb():
    i = 0
    while i<50:
        i = i + 10
        time.sleep(0.5)
        print('B:', i)

a = threading.Thread(target=aa)  # 建立新的執行緒
b = threading.Thread(target=bb)  # 建立新的執行緒

a.start()   # 啟用執行緒
a.join()    # 加入等待
b.start()   # b要等a執行完，才啟用執行緒

'''輸出結果
A: 1
A: 2
A: 3
A: 4
A: 5
B: 10    -->A結束後啟用B
B: 20
B: 30
B: 40
B: 50
'''

```

----

**範例三:** lock.acquire()和lock.release()
```
# test threading.Lock()

import threading
import time

def aa():
    lock.acquire()
    i = 0
    while i<5:
        i = i + 1
        time.sleep(0.5)
        print('A:', i)
    lock.release()

def bb():
    lock.acquire()
    i = 0
    while i<50:
        i = i + 10
        time.sleep(0.5)
        print('B:', i)
    lock.release()


a = threading.Thread(target=aa)  # 建立新的執行緒
b = threading.Thread(target=bb)  # 建立新的執行緒
lock = threading.Lock() # 啟用lock


a.start()   # 啟用執行緒
b.start()   # b因為被lock.acquire()，要等到a程序lock.release()才能運行

'''輸出結果
A: 1    -->A和B同時啟用lock.acquire()
A: 2
A: 3
A: 4
A: 5
B: 10    -->A結束後lock.release()，啟用B
B: 20
B: 30
B: 40
B: 50
'''
```

----

**範例四:** call command和timeout
```
# call command和timeout

import os
import threading


# 超時5秒則停止
def timeout():    
    print("timeout 5 sec")
    os._exit(0)

# 啟動thread平行程序，計時5秒
def thread():
    global t
    t = threading.Timer(5,timeout)
    t.start()
    return False

# command輸入
def cmd():
    cmd = input("$ ")
    if cmd == 'quit':
        t.cancel()
        return True
    print(f"You command is : {cmd}")
    t.cancel()
    return False

Flag = True
while Flag:
    
    if thread() or cmd():
        break
    

```

----

**範例五:** queue()和call command和timeout

```
# 偽代碼

cmd.start()
timeout.start()

if timeout:
    cmd.stop()
```
策略是
1. def()計時方法，來監測是否timeout
2. 用input()代替command，將輸入命令丟到queue
3. 用thread執行，計時timeout()和input()
4. if timeout()，則結束程式

```
# call command on queue和timeout

import os
import threading
import queue


# 超時5秒則停止
def timeout():    
    print("timeout 5 sec")
    os._exit(0)

# 同步計時5秒
def timethread():
    global tt
    tt = threading.Timer(5,timeout)
    tt.start()
    return False

# 執行queue裡的cmd指令
def workthread(q):
    while True:
        item = q.get()
        if item is None:
            break
        print(item)

# command輸入
def cmd():
    cmd = input("$ ")
    tt.cancel()
    if cmd == 'quit':
        return True
    
    q.put(f"You command is : {cmd}")
    return False

# 建立queue
q = queue.Queue()

# 啟動workthread平行程序，負責執行cmd指令
worker = threading.Thread(target=workthread, args=(q,))
worker.start()

while True:
    
    if timethread() or cmd():
        break
    
# workthread()結束
q.put(None)
worker.join()

```

----

## 補充

我對Process和Thread概念理解是，

Process是在不同記憶體空間下共同執行多行緒
Thread是在同個記憶體空間裡執行多行緒

Process就像國道上的3條道路，3台車在不同線道行駛。
Thread是在只有1條道路上，3台車同時向前行駛，因為共用同一條馬路，如果遇到車禍(互搶資源、死結)就掰掰了