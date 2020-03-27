---
layout: post
title: 安全 越权访问
categories: blog
description: 越权访问漏洞修复
keywords: security, bug
---

# 安全BUG: 越权访问

> 近期生产上出现越权访问的安全问题, 之前一直知道该安全问题,但是没怎么研究过, 现在正好有机会研究一下.

## 漏洞场景

前言:商户有唯一标识merNo, 存储在session中, 但商户拥有不定数量的店铺, 每个店铺使用一个店铺ID标识,

场景: 商户给店铺设置昵称,  只需要传两个参数, 一个是店铺ID,一个是昵称字符串,

修改时根据上传店铺ID找到对应记录, 后端未校验该店铺是否属于当前商户.

## 相关代码


```java
@PostMapping(value = "addChannelNickname")
@ResponseBody
public Map<String, Object> addChannelNickname(
    @RequestParam("channelId") Long channelId, 
    @RequestParam("channelNickname") String channelNickname) {

        // ------------------------------------
        // 越权修改漏洞 add by zhangqd 20190902
        Long merNo = UserUtils.getCurrentMer(null).getMerNo();
        ChannelInfoExample infoExample = new ChannelInfoExample();
        infoExample.createCriteria().andMerNoEqualTo(merNo);
        List<ChannelInfo> infos = channelInfoService.selectByExample(infoExample);

        List<Long> chs = infos.stream()
        .map(ChannelInfo::getChannelId)
        .collect(Collectors.toList());
        if (!chs.contains(channelId)) {
            map.put(SUCCESS, Status.FAIL);
            return map;
        }
        // ------------------------------------

        ChannelInfo channelInfo = new ChannelInfo();
        channelInfo.setChannelId(channelId);
        channelInfo.setChannelNickname(channelNickname);
        try {
            shopInfoService.updateByPrimaryKeySelective(channelInfo);
            map.put(SUCCESS, Status.SUCCESS);
        } catch (Exception e) {
            // e.printStackTrace();
            map.put(SUCCESS, Status.FAIL);
        }
        return map;
    }

```

## 修复方案

知道了相关场景, 修复方案也就确定了, 上方代码斜线注释块即为修复方案.

先查找该商户存在的店铺, 比较当前上送店铺ID 是否属于该商户,

属于执行正常逻辑, 不属于返回相关错误信息 .

## 进阶

> 该场景是由安全人员帮我们找出来的漏洞, 作为开发人员, 我们怎么确定相关场景, 在开发阶段就避免出现这样的问题?

参考链接 :

[【技术分享】聊聊越权那些事儿](https://www.anquanke.com/post/id/84892)

[水平越权访问与垂直越权访问漏洞](https://blog.csdn.net/u012068483/article/details/89553797)
> 越权类型:
- 垂直越权 : 访问自己没有权限的资源
- 水平越权 : 访问自己拥有权限的资源, 但修改了关键标识, 导致访问的是别人的信息
- 上下文越权 : 某个程序需要执行3个步骤，而你却可以跳过其中某个步骤

> 正常流程: 登录—>提交请求—>验证权限—>数据库查询—>返回结果

越权通常发生在提交请求到数据库查询的过程, 当没有对本次的请求做好权限验证的时候, 就可能发生越权.

> 主要形式:  service/getUserOrder?orderID=123123  HTTP/1.1

多为这种直接对象引用的形式, 其他类型的如多阶段功能, 也会是需要注意的功能点

## 项目架构阶段

1. 水平: 用户信息从 服务器session中获取，id相关增删改查校验所属用户
2. 垂直: 责任链的方式  在交易请求进来时 对 所属交易权限 跟session进行对比
3. 上下文: 只有从业务方多考虑, 一般发生在重置密码这样的多阶段功能
