# Modou TP API

![meme.jpg](meme.jpg)

But we did. :D

## 什么是魔豆触屏 API

其实大家所看到的屏幕上的应用，都是一个个的可执行程序，他们都被存放在 `/system/apps/tp/bin` 下

首先，我们需要 ssh 到魔豆路由器中，然后执行以下命令：

    $ ll /system/apps/tp/bin/

输出为：

```
-rwxrwxr-x    1 500      500         10724 Jul  9 11:00 about
-rwxrwxr-x    1 500      500         15064 Jul  9 11:00 advancedAuth
-rwxrwxr-x    1 500      500         17368 Jul  9 11:00 clock
-rwxrwxr-x    1 500      500         23992 Jul  9 10:58 convbdf
-rwxrwxr-x    1 500      500         10388 Jul  9 11:00 custom
-rwxrwxr-x    1 500      500         22744 Jul  9 11:00 device-list
-rwxrwxr-x    1 500      500         13108 Jul  9 11:00 expert-mode
-rwxrwxr-x    1 500      500         10052 Jul  9 11:00 fangcengwang
-rwxrwxr-x    1 500      500          8332 Jul  9 11:00 flash-system
-rwxrwxr-x    1 500      500         11400 Jul  9 11:00 internet-auth
-rwxrwxr-x    1 500      500          5700 Jul  9 11:00 loadingDemo
-rwxrwxr-x    1 500      500         14400 Jul  9 11:00 mochuiLauncher
-rwxrwxr-x    1 500      500         18560 Jul  9 11:00 moguling
-rwxrwxr-x    1 500      500          5340 Jul  9 11:00 msgboxDemo
-rwxrwxr-x    1 500      500        396440 Jul  9 11:00 nano-X
-rwxrwxr-x    1 500      500         59364 Jul  9 11:00 networkGuide
-rwxrwxr-x    1 500      500         12500 Jul  9 11:00 networkSetting
-rwxrwxr-x    1 500      500         21372 Jul  9 11:00 pinlock
-rwxrwxr-x    1 500      500          5256 Jul  9 11:00 screenSaver
-rwxrwxr-x    1 500      500         17256 Jul  9 11:00 system-control
-rwxrwxr-x    1 500      500         10256 Jul  9 11:00 system-help
-rwxrwxr-x    1 500      500         16556 Jul  9 11:00 system-status
-rwxrwxr-x    1 500      500         25488 Jul  9 11:00 system-update
-rwxrwxr-x    1 500      500          9960 Jul  9 11:00 systemMenu
-rwxrwxr-x    1 500      500          7420 Jul  9 11:00 testInputmethod
-rwxrwxr-x    1 500      500          6392 Jul  9 11:00 upload-log
-rwxrwxr-x    1 500      500         23776 Jul  9 11:00 wanSetting
-rwxrwxr-x    1 500      500         21364 Jul  9 11:00 wifiSetting
```

他们分别是什么呢？有兴趣的小伙伴们可以自己进行实验，在这里就不一一赘述了。

我们接下来会基于 UNIX 哲学，把触屏上的应用进行一轮重构和原子化，把应用们拆成一个个小型的，具有明确输入输出定义的应用，这样各位开发者们就可以对他们进行随意的组合，从而非常快速地写出触屏上面的应用了！

目前，我们暂时只开放了一个触屏应用，就是 `/system/apps/tp/bin/custom`。这个名为 `custom` 的应用应该怎么使用呢？

## 第一个触屏 API：名为 custom 的神秘应用

我们尝试在命令行下执行它：

    $ /system/apps/tp/bin/custom

得到的输出是：

```
(1959.710280)/mem/mochui-rom/mochui-sys/microwin/src/mochui/custom.c-309: Wrong arguments!
custom json_config_file
```

hmm... 有点意思。这个 `json_config_file` 又是什么呢？我们发现 `/system/apps/tp/conf/custom.conf` 这个文件，内容如下：

```json
{
  "title" : "hdns",
  "content" : "hdns是一个啥啥啥啥blablabla...",
  "button1" : {
    "txt" : "启动",
    "cmd" : "/data/apps/hdns/sbin/hdns.sh start",
    "code" : {
      "0" : "执行成功",
      "1" : "执行错误1",
      "2" : "执行错误2"
    }
  },

  "button2" : {
    "txt" : "停止",
    "cmd" : "/data/apps/hdns/sbin/hdns.sh stop",
    "code" : {
      "0" : "服务已经停止",
      "1" : "执行错误1",
      "2" : "执行错误2"
    }
  },

  "button3" : {
    "txt" : "重启",
    "cmd" : "/data/apps/hdns/sbin/hdns.sh restart",
    "code" : {
      "0" : "已经重启",
      "1" : "执行错误1",
      "2" : "执行错误2"
    }
  }
}
```

Bingo! 所以呢，我们尝试性地敲下：

    $ /system/apps/tp/bin/custom /system/apps/tp/conf/custom.conf

结果呢屏幕上出现了什么？？

![custom.png](custom.png)

这个时候再回头去看 `/system/apps/tp/conf/custom.conf` 的内容，大家明白了么？ XD

另外，还有一个文件也值得注意，那就是 `/system/apps/tp/conf/tp_apps.conf`，我们来看它的内容：

```json
{
 "app_num": 8,
 "apps":[
    { "name" : "网络状态",
      "icon" : "/system/apps/tp/images/appicon_status_normal.png",
      "iconPressed" : "/system/apps/tp/images/appicon_status_active.png",
      "exec" : "/system/apps/tp/bin/system-status",
      "msgNum" : 4
    },   
    { "name" : "设备列表",
      "icon" : "/system/apps/tp/images/appicon_devices_normal.png",
      "iconPressed" : "/system/apps/tp/images/appicon_devices_active.png",
      "exec" : "/system/apps/tp/bin/device-list",
      "msgNum" : 4
    },  
    { "name" : "时钟",
      "icon" : "/system/apps/tp/images/appicon_time_normal.png",
      "iconPressed" : "/system/apps/tp/images/appicon_time_active.png",
      "exec" : "/system/apps/tp/bin/clock",
      "msgNum" : 4
    },
    { "name" : "网络设置",
      "icon" : "/system/apps/tp/images/appicon_networkConfig_normal.png",
      "iconPressed" : "/system/apps/tp/images/appicon_networkConfig_active.png",
      "exec" : "/system/apps/tp/bin/networkSetting",
      "msgNum" : 4
    },
    { "name" : "极客模式",
      "icon" : "/system/apps/tp/images/appicon_expertMode_normal.png",
      "iconPressed" : "/system/apps/tp/images/appicon_expertMode_active.png",
      "exec" : "/system/apps/tp/bin/expert-mode",
      "msgNum" : 4
    },   
    { "name" : "系统更新",
      "icon" : "/system/apps/tp/images/appicon_update_normal.png",
      "iconPressed" : "/system/apps/tp/images/appicon_update_active.png",
      "exec" : "/system/apps/tp/bin/system-update",
      "msgNum" : 4
    },
    { "name" : "防蹭网",
      "icon" : "/system/apps/tp/images/appicon_app_2-factorAuth_normal.png",
      "iconPressed" : "/system/apps/tp/images/appicon_app_2-factorAuth_active.png",
      "exec" : "/system/apps/tp/bin/fangcengwang",
      "msgNum" : 4
    },  
    { "name" : "帮助反馈",
      "icon" : "/system/apps/tp/images/appicon_feedback_normal.png",
      "iconPressed" : "/system/apps/tp/images/appicon_feedback_active.png",
      "exec" : "/system/apps/tp/bin/system-help",
      "msgNum" : 4
    },  
    { "name" : "游戏加速",
      "icon" : "/system/apps/tp/images/appicon_gameAcc_normal.png",
      "iconPressed" : "/system/apps/tp/images/appicon_gameAcc_active.png",
      "exec" : "/system/apps/tp/bin/game-speedup",
      "msgNum" : 4
    },   
    { "name" : "初始化向导",
      "icon" : "/system/apps/tp/images/appicon_settingGuide_normal.png",
      "iconPressed" : "/system/apps/tp/images/appicon_settingGuide_active.png",
      "exec" : "/system/apps/tp/bin/networkGuide",
      "msgNum" : 4
    }   
 ]
}
```

我们把 `app_num` 的值改成 9，大家又看到了什么？

![launcher.png](launcher.png)

