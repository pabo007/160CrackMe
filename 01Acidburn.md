分析环境：win7（32）专业版
工具：OD PEID
现在重新的进行步骤，拿到一个程序最开始要查壳，这是一个Delphi写的程序，没有壳

运行一下程序，弹出一个框

       点击确定来进一步观察，发现这个程序有两个地方，一处是序列号和密码，另外一处是填一个序列号，使用OD来附加，先分析这个有用户名和密码的窗口，输入测试的用户名和密码

下api断点，程序被断下来，断下来了MessageBoxA()

使用字符串分析的方法发现这些信息都被存储在了全局数据区，字符串法失效，接下来查看堆栈的调用，好像登录的密码直接就出现了

密码填进去之后注册成功非常的简单

但是做到了这一步之后，我们并不满足，现在来还原它的代码，或者写出注册机
在下面的堆栈窗口中，找到这个地址然后下断，这一步没用

在我们之前下过的断点api断点中被断下来

这个思路暂时走不通，也是失败的



重新开始运行，验证密码弹出窗口，F12暂停，进入堆栈窗口第一个明显太大太可能是调用，选择下面一个窗口，右键

找到调用的函数下断点，分析上下并没有可疑的跳转，下面就在函数的开头下断点

重新验证将程序跑起来，程序断在0042A170那里，继续按照刚才的方法来进入堆栈，栈回溯显示调用，下断点并将原来的取消掉，通过这里的上下文可以看到这里就应该极有可能是主程序了，关键字符串也在这里

老办法在程序的开栈帧那里下断点单步，分析如下



经过一系列的操作之后

继续单步

#include <stdio.h>
#include <string.h>

int main()
{
        char *str = "CW-xxxx-CRACKED";
        int uName;
        printf("请输入用户名：");
        //scanf("%s", uName);
        uName = getchar();
        if (uName > 0x21)
        {
                uName *= 0x29;
                uName *= 2;
                //sprintf(str + 3, "%4d", uName[0]);
        }
        else
        {
                printf("Error");
        }
        printf("%4d", uName);
        return 0;
}
