# Unity实现根据日期锁定逻辑功能

##### 前言：在做我们项目的时候，或者是接一些外包私活的时候，都会遇到回款难的问题。遇到这种问题，除了死缠烂打，还有就是使用一定的技术，在我们程序中加入一些猫腻，也就是很据日期给程序添加锁定功能。当甲方客户不要碧莲的想白嫖的时候，忽然有一天程序不能用了

#### 1.在StreamingAssets文件夹下添加Config.txt文件，添加配置文件功能（配置文件功能在之前的博客介绍过，在这里不赘述），配置文件里的两个参数是用日期转成的16进制数，转成16进制可以避免其他人随意改程序锁定日期。如配置文件内容下图所示：

![description](https://connect-cn-cdn-public-prd.unitychina.cn/h1/20201126/p/images/ded34c83-5a93-441d-99b4-12a137064ba7______20201126234333.png)

#### 2.编写LockTest.cs脚本，实现根据日期锁定程序功能的基本功能，代码如下所示：

```
using System; 
using System.Collections; 
using System.Collections.Generic;
using UnityEngine;
public class LockTest : MonoBehaviour 
{ 
//限定最小时间字符串
string minLockTime; 
//到期时间字符串 
string maxLockTime; 
//最小时间整形 
int minTime = 0; 
//到期时间整型
int maxTime = 0; 
//配置文件管理类
public ConfigTest configTest; 
//游戏管理类 
public ThisGameManager gameManager;

// Use this for initialization
void Start()
{
    BeginString();
    StartCoroutine(ToLockThis());
}

/// <summary>
/// 初始化数据
/// </summary>
void BeginString()
{
    minLockTime = configTest.dic["激活码"]["min"];
    maxLockTime = configTest.dic["激活码"]["max"];
}

/// <summary>
/// 根据日期锁定
/// </summary>
/// <returns></returns>
IEnumerator ToLockThis()
{
    yield return new WaitForSeconds(0.1f);
    ToChangeTen();
    yield return new WaitForSeconds(0.1f);
    BeginToLock();
}

/// <summary>
/// 十六进制字符转换为十进制字符
/// </summary>
void ToChangeTen() 
{
    minTime = Convert.ToInt32(minLockTime,16);
    maxTime = Convert.ToInt32(maxLockTime, 16);
}

/// <summary>
/// 根据日期锁定游戏
/// </summary>
void BeginToLock()
{
    int todayTime = int.Parse(System.DateTime.Now.ToString("yyyyMMdd"));
    if ((todayTime >= minTime) && (todayTime <= maxTime))
    {
        gameManager.CanSend = true;
    }
    else
    {
        gameManager.CanSend = false;
    }
}
}
```

#### 3.将该脚本挂载到场景中的lockTest物体上，将configTest赋值到该物体上，如下图所示：

![description](https://connect-cn-cdn-public-prd.unitychina.cn/h1/20201126/p/images/1379ca69-ad69-441b-9bfa-021598c1fdcb______20201126234519.png)

#### 4.编写ThisGameManager.cs脚本，编写调用锁定功能的方法，代码如下所示： 

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ThisGameManager : MonoBehaviour 
{ 
//是否可以发生数据
public bool CanSend = true;

/// <summary>
/// 发送数据
/// </summary>
public void ToSendThis() 
{
    if (CanSend) 
    {
        Debug.Log("已触发！");
    }
}
}
```

#### 5.将该脚本挂载到场景中的物体上，将其赋值到lockTest物体上，如下图所示：

![description](https://connect-cn-cdn-public-prd.unitychina.cn/h1/20201126/p/images/e6f72a8a-868e-48d5-a404-2c55c2ae670d______20201126234633.png)

#### 6.将ThisGameManager中的ToSendThis方法挂载到场景中的按钮上，如下图所示：

![description](https://connect-cn-cdn-public-prd.unitychina.cn/h1/20201126/p/images/4d344141-6ca9-410f-a1f2-360117bec1d8______20201126234716.png)

####  7.运行项目，连续点击按钮，发现打印控制台打印出了字符串，如下图所示：

![description](https://connect-cn-cdn-public-prd.unitychina.cn/h1/20201126/p/images/e076e5ca-d425-4e88-a883-b4f6abc9f98f_20201126194013291.gif)

####  8.将配置文件中的参数改为16进制的其他日期，如20201001~20201020的16进制参数，保存，如下图所示：

![description](https://connect-cn-cdn-public-prd.unitychina.cn/h1/20201126/p/images/1840f81f-cecb-4737-9d28-818a4665c65e______20201126234835.png)

#### 9.运行工程，点击按钮，发现程序打印字符串的功能已经被锁定了，因为日期不在区间内，如下图所示：

![description](https://connect-cn-cdn-public-prd.unitychina.cn/h1/20201126/p/images/fd31b7e6-89d4-4328-8d2a-b255657bda1d______20201126234908.png)