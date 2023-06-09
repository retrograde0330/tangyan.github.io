# fork 例

```py
# learnfork.py
# 我们在shell中使用`python3 learnfork.py`执行这段Python代码

import os
import time

# 这个Python文件对应的进程被shell创建并开始执行
# 其实这个进程就是一个Python3的解释器

pid = os.fork()
# 注意这里fork()只被Python文件对应的进程调用了一次
# 在fork()执行的过程中 当前进程的空间被复制了一份给子进程
# fork()执行结束之后 pid会分别在父进程和子进程返回
# 子进程得到的是0 父进程得到的是子进程的pid

# 此时已经有两个进程在同时运行 注意这里同时说的是时间片轮转两个程序都会参与调度运行
if pid < 0:  # fork失败
    print("fork失败 退出当前进程")
    exit()

if pid >= 1:  # 当前的程序是父进程 成功创建了子进程 而且拿到了子进程的pid
    print(f"[父进程] 我是父进程(pid{os.getpid()}) 创建了子进程(pid{pid}) 由shell(pid{os.getppid()})创建")
    print("[父进程] 干自己的事情...")
    time.sleep(5)
    # pid, result = os.waitpid(pid, 0)
    # # https://docs.python.org/3/library/os.html#os.waitpid
    # print(f"[父进程] 子进程(pid{pid})退出 返回值为{result >> 8}")
    print("[父进程] 退出")
    exit(0)
elif pid == 0:  # 当前的程序是新创建出的子进程 可以开始做一些子进程的事情了
    print(f"[子进程] 我是子进程(pid{os.getpid()}) 由父进程(pid{os.getppid()})创建")
    print("[子进程] 干自己的事情...")
    time.sleep(10)
    print("[子进程] 退出")
    exit(0)

# 父进程和子进程各自执行完自己的三个print会退出
# 但其实并不是这样的
# 如果父进程先结束 父进程结束后子进程按理说会成为僵尸进程 除非被系统接管
# 如果子进程先结束 父进程应该对应的回收子进程（learnfork.py并没有考虑回收） 父进程结束后子进程按理说僵尸进程 除非被系统接管
# 嘛 也就是说这个程序 不考虑操作系统 无论如何子进程都会成为僵尸进程

# 当父进程`sleep(5)`而子进程`sleep(10)`的输出情况如下:
#
# $ python3 learnfork.py # 1
# [父进程] 我是父进程(pid92336) 创建了子进程(pid92337) 由shell(pid90821)创建 # 2
# [父进程] 干自己的事情... # 3
# [子进程] 我是子进程(pid92337) 由父进程(pid92336)创建 # 4
# [子进程] 干自己的事情... # 5
# [父进程] 退出 # 6
# $ [子进程] 退出 # 7
#
# 第1行为我们手敲的 回车之后
# 第2-5行瞬间输出
# 等5秒之后第6行输出
# 这时shell认为它fork的Python3解释器已经结束运行了 然后就在第7行输出了一个`$`
# 等5秒之后第7行输出子进程结束的信息 注意这说明父进程结束运行之后子进程还在运行 这应该是操作系统进行了接管

# 作修改添加父进程回收子进程的代码:
# 在父进程最后一行代码前添加`pid, result = os.waitpid(pid)`即可让父进程等待子进程结束运行
# 取消父进程代码中的三行注释
# 输出结果如下:
#
# $ python3 learnfork.py
# [父进程] 我是父进程(pid93225) 创建了子进程(pid93226) 由shell(pid90821)创建
# [父进程] 干自己的事情...
# [子进程] 我是子进程(pid93226) 由父进程(pid93225)创建
# [子进程] 干自己的事情...
# [子进程] 退出
# [父进程] 子进程(pid93226)退出 返回值为8
# [父进程] 退出
```
