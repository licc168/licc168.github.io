---
layout:     post
title:      sublime3安装Evernote插件
subtitle:   sublime3安装Evernote插件 支持markdown语法
keyword:     sublime,sublime3安装Evernote插件,Evernote,markdown
description : sublime3安装Evernote插件 支持markdown语法
date:       2017-07-24
author:     licc
header-img: img/post_bg_debug.png
catalog: true
tags:
    - sublime
    - evernote
---

## 安装插件
####  安装markdown插件
  - MarkdownEditing 
  - OmniMarkupPreviewer
 
  [以上两款插件详细安装方式参考资料](http://www.jianshu.com/p/335b7d1be39e)
  
####  安装evernote插件
   Shift+Ctrl+P > 输入pci  > evernote 安装
   
## 配置evernote 用户信息
  1. **获得evernote api接口 Developer Tokens**<br>
   Preferences -> Package Settings ->  Evernote -> Reconfigure Authorization
   会在浏览器中打开 https://www.evernote.com/api/DeveloperToken.action
   如果是中国区 印象笔记用户请直接打开  https://app.yinxiang.com/api/DeveloperToken.action
       

   
   2. **设置token信息**
    Preferences -->Package Settings --> Evernote -->Settings User
      
        ```json
        {
         "noteStoreUrl": "***",
         "token": "***"
        }
        ```
      
   ![](https://www.iamle.com/wp-content/uploads/2014/11/20141101173120.png)
     
## 配置evernote 快捷键

Preferences--> Key Bindings-User

**官方给出的快捷键配置及介绍**

The plugin does not install keymaps, if you wish you may add a variation of the following to your user keymaps:
```json
{ "keys": ["super+e"], "command": "show_overlay", "args": {"overlay": "command_palette", "text": "Evernote: "} },
{ "keys": ["ctrl+e", "ctrl+s"], "command": "send_to_evernote" },
{ "keys": ["ctrl+e", "ctrl+o"], "command": "open_evernote_note" },
{ "keys": ["ctrl+e", "ctrl+u"], "command": "save_evernote_note" },
```

you can also overwrite the standard “save” bindings for Evernote notes as follows:
```json
{ "keys": ["ctrl+s"], "command": "save_evernote_note", "context": [{"key": "evernote_note"}] },
{ "keys": ["ctrl+s"], "command": "send_to_evernote", "context": [{"key": "evernote_note", "operator": "equal", "operand": false}, {"key": "selector", "operator": "equal", "operand": "text.html.markdown.evernote"}] },
```

注：super代表Windows键或者Command键(Mac)

问题1：Windows键在Win8系统全被系统截获响应，自定义快捷键应尽量避免。显然官方给出的第一个快捷键在Windows8下不会有响应。
问题2：新建一篇笔记才有send的必要，已经新建好的笔记只需要save就可以了。所以如果要使用官方给出的快捷键，尽量不要直接将两者拼合使用，尽管不会出现什么问题。

**我的配置**
```json
[
    { "keys": ["ctrl+shift+e"], "command": "show_overlay", "args": {"overlay": "command_palette", "text": "Evernote: "} },
    { "keys": ["ctrl+e", "ctrl+s"], "command": "send_to_evernote" },
    { "keys": ["ctrl+e", "ctrl+o"], "command": "open_evernote_note" },
    { "keys": ["ctrl+e", "ctrl+n"], "command": "new_evernote_note" },
    { "keys": ["ctrl+s"], "command": "save_evernote_note", "context": [{"key": "evernote_note"}] },
    { "keys": ["ctrl+s"], "command": "send_to_evernote", "context": [{"key": "evernote_note", "operator": "equal", "operand": false}, {"key": "selector", "operator": "equal", "operand": "text.html.markdown.evernote"}] }

]
```
- 使用ctrl+shift+e直接调用与Evernote相关的Menu。不常用的此插件功能可以通过此快捷键调出menu后查询使用。
- 加入了新建笔记快捷键（"ctrl+e", "ctrl+n" 连续按键，其他绑定两个键的使用方式相同）
- 新建一篇笔记，由于没有保存前还不是evernote_note的环境（"context": [{"key": "evernote_note"}]），所以需要send的快捷键。一旦创建成功或者是编辑以前的笔记，才有save的需求，所以有
```json
{ "keys": ["ctrl+s"], "command": "save_evernote_note", "context": [{"key": "evernote_note"}] }
```
的配置

备注：使用新建快捷键之后，使用ctrl+s无效。所以怀疑官方给出的最后一个快捷键（send）配置没有生效。只能通过 "ctrl+e", "ctrl+s" ，暂时保留没有做深入研究。
深入研究资料保留： Sublime快捷键配置官方介绍

还有一个比较怪异的现象是：
如果我把keys的绑定都绑定成一个键，即替换sublime本身的ctrl+s、 ctrl+n、 ctrl+o ，放弃先敲一个ctrl+e的策略。
ctrl+s可以看到效果， ctrl+n、ctrl+o均无效。
