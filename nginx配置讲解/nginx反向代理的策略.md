# 一：循环（默认）

```
配置upstream
   upstream example {
      server 10.7.182.110:8080;
      server 10.7.182.87:8080;
   }
   配置负载均衡
   location ~ \.jpg$ {
           proxy_pass   http://example;
   }
```

# 二：权重

```
#负载均衡的配置
  upstream example {
     server 10.7.182.110:8080 weight=2;
     server 10.7.182.87:8080 weight=1;
  }
 配置负载均衡
     location ~ \.jpg$ {
           proxy_pass   http://example;
     }
```

# 三：ip_hash

ip_hash机制能够让某一客户机在相当长的一段时间内只访问固定的后端的某台真实的web服务器,这样会话就会得以保持,在网站页面进行login的时候就不会在后面的web服务器之间跳来跳去了,也不会出现登录一次的网站又提醒重新登录的情况.

```
#负载均衡的配置
   upstream example {
      ip_hash;
      server 10.7.182.110:8080;
      server 10.7.182.87:8080;
   }
配置负载均衡
    location ~ \.jpg$ {
           proxy_pass   http://example;
    }
```

