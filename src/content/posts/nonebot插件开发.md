---
title: nonebot机器人插件
tags: 
  - Python
category: Python
cover: ''
published: 2025-09-21 10:42:00
---

前几日由于群友需要qq机器人来爬取jm本子，遂将引进该“尖端技术”的重任委托于我

经过我不屑的寻找，终于发现了好用的bot——nonebot



在此之前我也尝试过别的bot，但是感觉还是nonebot方便快捷，而且api文档很详细，最终选择了他

# 插件开发思路

1. 获取车牌并爬取jm
2. 下载到本地并合并为pdf
3. 控制机器人发送pdf
4. 记录车牌号并缓存，下次优先检查缓存发送

## 爬取jm

我这里直接使用了jmcomic这个模块

使用起来很简单

不过我只是临时用用所以并没有处理多章节下载合并的情况

## 合并pdf

我使用了PIL的Image库来处理图片

## 发送

这里需要调用bot.callapi

```python
await bot.call_api(
            "upload_group_file",
            group_id=event.group_id,
            file=f"{path}/{jmid}.pdf",
            name=f"{jmid}.pdf",
)
```

由于一些原因导致我一开始callapi死活用不了，后来发现是前面一个异步函数的问题

nonebot里进行任何操作最好加上await，不然容易堵塞

## 缓存

下载后记录名字与车牌写进json

等到下次使用时进行检测，如果有缓存直接发送缓存，也可以手动清除缓存

## 完整插件

由于是临时写的，代码好多地方都不规范

而且jmcomic多个章节下载我也没处理

反正能用就行

```python
"""
Author: adeptmicors279
Date: 2025-07-29 18:48:19
LastEditTime: 2024-08-08 23:15:31
LastEditors: 279
Description: jm
"""
import shutil
import jmcomic
from jmcomic import *
from nonebot.params import ArgPlainText
from nonebot.adapters import Message
from nonebot.params import CommandArg
from nonebot.rule import to_me
from nonebot import on_regex, on_command
from nonebot.typing import T_State
from nonebot.adapters.onebot.v11 import GroupMessageEvent, Bot, Message
from nonebot.matcher import Matcher
from nonebot.adapters.onebot.v11 import MessageSegment
from PIL import Image
import os
import json

def images_to_pdf(image_folder, output_path):

    # 获取文件夹中所有图片文件
    image_files = [f for f in os.listdir(image_folder)]

    # 存储Image对象的列表
    image_list = []

    # 按文件名顺序读取图片
    for file in image_files:
        file_path = os.path.join(image_folder, file)
        try:
            # 打开图片并转换为RGB模式
            img = Image.open(file_path)
            if img.mode != "RGB":
                img = img.convert("RGB")
            image_list.append(img)
        except Exception as e:
            print(f"无法处理文件 {file}: {str(e)}")

    if not image_list:
        print("未找到有效图片")
        return

    # 使用第一张图片创建PDF
    first_image = image_list[0]
    # 将其余图片附加到PDF中
    remaining_images = image_list[1:]

    # 保存为PDF
    first_image.save(output_path, save_all=True,
                     append_images=remaining_images)

    # 关闭所有图片
    for img in image_list:
        img.close()

    print(f"PDF已成功生成: {output_path}")

title = None
Test = on_regex(pattern=r"^测试$", priority=2, rule=to_me())
jm_get = on_command("jm", priority=1, rule=to_me())
test_send_file = on_command("test_send", priority=10, rule=to_me())
help = on_regex(pattern=r"^帮助$",priority=1)
jm_clean_cache = on_command("jmn",priority=3,rule=to_me())


@Test.handle()
async def Test_send(bot: Bot, event: GroupMessageEvent, state: T_State):
    msg = "Bot启动正常"
    await Test.finish(message=Message(msg))


@jm_get.handle()
async def jm_get_send(bot: Bot, event: GroupMessageEvent, state: T_State, args: Message = CommandArg()):
    if jmid := args.extract_plain_text():
        await jm_get.send(
            message=Message(f"正在获取JMID为{jmid}的comic\n获取时间较长,优先检查缓存内容")
        )
        
        path = os.getcwd() # 设定路径        
        if os.path.exists(str(jmid)+'.pdf'):
            await jm_get.send(message=Message("检查到缓存内容"))
            await bot.call_api(
            "upload_group_file",
            group_id=event.group_id,
            file=f"{path}/{jmid}.pdf",
            name=f"{jmid}.pdf",
        )                
            await jm_get.finish(message=Message("获取完成"))
            return 0
        
        
        if not os.path.exists(str(jmid)+'.pdf'):
            await jm_get.send(message=Message("未检测到缓存内容,联网下载"))
            client = JmOption.default().new_jm_client()
            # 设置client
            page = client.search_site(search_query=f"{jmid}")
            album: JmAlbumDetail = page.single_album

            jmcomic.download_album(int(jmid))
            images_to_pdf(image_folder=album.title + r"/",
                        output_path=f"./{jmid}.pdf")
            
            # 记录缓存内容
            with open (r'./src/plugins/jm/name_id.json','r')as cache:
                dic = json.load(cache)
                dic[str(album.title)] = int(jmid)             
            with open (r'./src/plugins/jm/name_id.json','w')as new_cache:
                json.dump(dic,new_cache)
                
            # await jm_get.send(message=Message(str(event.group_id)))
            await bot.call_api(
                "upload_group_file",
                group_id=event.group_id,
                file=f"{path}/{jmid}.pdf",
                name=f"{jmid}.pdf",
            )
            await jm_get.finish(message=Message("获取完成"))
            return 0            

    else:
        msg = "未识别到jm车牌号"
        await jm_get.finish(message=Message(msg))


@jm_clean_cache.handle()
async def jm_get_new(bot: Bot, event: GroupMessageEvent):
    await jm_clean_cache.send(
        message=Message(f"该操作会刷新缓存,请稍等...")
    )
    path = os.getcwd() # 设定路径
    with open(r'./src/plugins/jm/name_id.json','r')as cache:
        dic = json.load(cache)
        name_cache = dic.keys()
        jmid_cache = dic.values()
        for i in name_cache:
            shutil.rmtree(str(path+rf"/{i}"))
        for i in jmid_cache:
            os.remove(str(i)+".pdf")
        dic = {}
        json.dump(dic,cache)
        
    await jm_clean_cache.finish(message=Message("缓存删除成功"))
        
        

@test_send_file.handle()
async def test_send_file(bot: Bot, event: GroupMessageEvent, state: T_State):
    await bot.call_api(
        "upload_group_file",
        group_id=event.group_id,
        file="./1201023.pdf",
        name="1201023.pdf"
    )


@help.handle()
async def help_menu(bot: Bot, event: GroupMessageEvent, state: T_State):
    msg = "输入/jm xxxxx 即可获取comic\n所有功能需要@机器人才能完成"
    await help.finish(message=Message(msg))

```



