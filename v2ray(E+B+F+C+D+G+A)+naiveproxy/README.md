介绍：

利用 caddy 支持 SNI 分流特性，对 Xray\v2ray（vless+tcp+tls）、Xray\v2ray（trojan+tcp+tls）、caddy（https server） 进行 SNI 分流（四层转发），实现除 Xray\v2ray kcp 外共用443端口。另外 caddy 同时为 Xray\v2ray（vless+tcp+tls）与 Xray\v2ray（trojan+tcp+tls） 提供回落服务，为 Xray\v2ray 的 h2c 与 gRPC 进行反向代理，为 naiveproxy 提供正向代理。包括应用如下：

1、E=vless+tcp+tls（回落/分流配置，tls由自己提供及处理。）

2、B=vless+ws+tls（tls由vless+tcp+tls提供及处理，不需配置；另可改成或添加其它WS类应用，参考对应的服务端单一应用配置示例。）

3、F=trojan+tcp+tls（回落/分流配置，tls由自己提供及处理。）

4、C=SS+v2ray-plugin+tls（tls由trojan+tcp+tls提供及处理，不需配置；另可添加其它WS类应用，参考对应的服务端单一应用配置示例。）

5、D=vless+h2c+tls（tls由caddy提供及处理，不需配置；另可改成或添加其它h2c类应用，参考对应的服务端单一应用配置示例。）

6、G=vless+grpc+tls（tls由caddy提供及处理，不需配置；另可改成或添加其它gRPC类应用，参考对应的服务端单一应用配置示例。）

7、A=vless+kcp+seed（可改成vmess+kcp+seed，或添加它。）

8、naiveproxy（带有forwardproxy插件的caddy才支持naiveproxy应用，否则仅上边应用。tls由caddy提供及处理。）

注意：

1、Xray 版本不小于 1.4.0 或 v2ray 版本不小于v4.36.2，才完美支持 gRPC 应用。

2、caddy 不小于 v2.2.0-rc.1 版才支持 h2c proxy，即支持 Xray\v2ray 的 h2c（gRPC） 反向代理。

3、caddy 支持 http/1.1 server 与 h2c server 共用一个端口或一个进程（Unix Domain Socket 应用）。

4、caddy 加 caddy-l4 插件定制编译的才可以实现 SNI 分流，目前仅支持使用 json 配置。

5、使用本人 Releases 中编译好的 caddy 文件，可同时支持SNI 分流、naiveproxy、h2c server、h2c proxy 及 PROXY protocol 等应用。

6、本示例采用的是 SNI 方式实现共用443端口，支持 Xray\v2ray（vless+tcp+tls）、Xray\v2ray（trojan+tcp+tls）、caddy（https server） 完美共存，支持各自特色应用，但需多个域名来标记分流。

7、本示例配置不要使用非 caddy（自带 ACME 客户端）的 ACME 客户端在当前服务器上申请与更新普通证书及密钥，因普通证书及密钥申请与更新需占用或监听80端口（或443端口），从而与当前应用端口冲突。

8、Xray 所需证书及密钥推荐使用 caddy 申请，配合 Xray（版本必须不低于v1.3.0）自动重载证书及密钥（OCSP Stapling），可实现证书及密钥申请与更新全自动化。

9、配置1：采用端口分流、端口回落\分流、端口转发。配置2：采用进程分流、进程回落\分流、端口转发。配置3：采用进程分流、进程回落\分流、端口转发，且启用了 PROXY protocol。

10、若有实际网站服务推荐采用 [v2ray(E+B+C+D+G+A)+caddy(N+T)+nginx\haproxy](https://github.com/lxhao61/integrated-examples/tree/main/v2ray(E%2BB%2BC%2BD%2BG%2BA)%2Bcaddy(N%2BT)%2Bnginx%5Chaproxy) 或 [v2ray(E+B+F+C+D+G+A)+naiveproxy+nginx\haproxy](https://github.com/lxhao61/integrated-examples/tree/main/v2ray(E%2BB%2BF%2BC%2BD%2BG%2BA)%2Bnaiveproxy%2Bnginx%5Chaproxy) 示例，否则 caddy 的压力可能过大。
