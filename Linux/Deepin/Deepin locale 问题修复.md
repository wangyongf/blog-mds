# Deepin Locale 问题修复

## 软件环境

1. Deepin Linux 15.6

## Deepin Locale Error

今天在打开 Deepin 准备更新应用的时候，出了点问题，所有软件都更新不了了，貌似是 libc 版本太高导致的，关于这个问题，我后续会开一篇文章单独介绍，今天主要是想谈谈另外一个问题：

```
Warning: Invalid locale (please review locale settings, this might lead to problems later):
locale::facet::_S_create_c_locale name not valid
```

## 如何解决

第一时间，当然是找 Google 爸爸。果真，找到了这篇文章：[Ubuntu locale error](https://blog.sbouafif.com/2018/04/19/ubuntu-locale-error/)

编辑 `/etc/environment` 和 `/etc/default/locale`，新增如下：

```
LANG="zh_CN.UTF-8"
LC_ALL="zh_CN.UTF-8"
```

然后保存之，然后重启一下系统，应该就没问题了~

## 后记

无
