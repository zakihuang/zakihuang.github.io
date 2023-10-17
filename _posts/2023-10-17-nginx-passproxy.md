---
layout: post
title: "nginx passproxy 配置"
date: 2023-08-14
categories: [api]
tags: [api, default value]
---
一、基本说明
在nginx中配置proxy_pass代理转发时，如果在proxy_pass后面的url加/，表示绝对根路径；如果没有/，表示相对路径，把匹配的路径部分也给代理走。

假设下面四种情况分别用 http://192.168.1.1/proxy/test.html 进行访问。


第一种：代理到URL：http://127.0.0.1/test.html
```
location /proxy/ {
  proxy_pass http://127.0.0.1/;
}
```

第二种（相对于第一种，最后少一个 / ）代理到URL：http://127.0.0.1/proxy/test.html

```
location /proxy/ {
  proxy_pass http://127.0.0.1;
}
```

第三种：代理到URL：http://127.0.0.1/aaa/test.html

```
location /proxy/ {
  proxy_pass http://127.0.0.1/aaa/;
}
```

第四种（相对于第三种，最后少一个 / ）代理到URL：http://127.0.0.1/aaatest.html

```
location /proxy/ {
  proxy_pass http://127.0.0.1/aaa;
}
```

乾坤例子
```
    # 运营端
    server {
        # 航信主入口
        listen       8000;
        server_name  127.0.0.1;

        # 航信主站
        location / {
            proxy_pass http://127.0.0.1:8082;
        }

        # 订单贷 => 子应用 => OrderFinanceOperationUI
        location /operaordercredit {
            proxy_pass http://127.0.0.1:1888/;
        }
        # 票据 => 子应用 => OperateCenterUI
        location /operabill {
            proxy_pass http://127.0.0.1:1890/;
        }

        # 电子账户 => 子应用 => AccountCenterUI
        location /operadzzh {
            proxy_pass http://127.0.0.1:1889/;
        }
    }
```
