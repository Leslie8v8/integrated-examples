介绍：

1、此配置为 Xray\v2ray trojan 协议，以 h2c 方式传输。

2、通过 caddy 前置（监听443端口）实现 h2c 的反向代理，tls 由 caddy 提供及处理，trojan+h2c+tls 应用。

原理：

默认流程：web client <----------- https（http/2） ----------> caddy（web server）  
匹配流程：Xray\v2ray client <------ h2c+tls（http/2） ------> caddy <-- h2c --> Xray\v2ray server

注意：

1、v2ray v4.31.0 版本及以后才支持 trojan 协议。

2、caddy 不小于 v2.2.0-rc.1 版才支持 h2c proxy，即支持 Xray\v2ray 的 h2c 反向代理。

3、本示例中 caddy 的 Caddyfile 格式配置与 json 格式配置二选一即可（效果一样）。支持自动 https，即自动申请与更新证书与私钥，自动 http 重定向到 https。

4、nginx 不支持 h2c proxy，故不能用 nginx 来实现 Xray\v2ray 的 h2c 反向代理。
