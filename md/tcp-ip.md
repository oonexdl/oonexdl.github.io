从 ping 说起



dns 解析：解析器请求名字服务器 => 命中名字服务器高速缓存 or 请求根名字服务器 => IP
封装 ICMP 包 + IP 首部 => 链路层帧，最终以比特流的形式进入 lan
根据 dst ip 寻址 => 搜索路由表，未匹配转发给局域网 gateway => gateway 路由表决定下一步选路???

                          +-------------------------+
               (public IP)|                         |
  {INTERNET}=============={     Router              |
                          |                         |
                          |         LAN switch      |
                          +------------+------------+
                                       | (192.168.0.1)
                                       |
                                       |              +-----------------------+
                                       |              |                       |
                                       |              |        OpenVPN        |  eth0: 192.168.0.10/24
                                       +--------------{eth0    server         |  tun0: 10.8.0.1/24
                                       |              |                       |
                                       |              |           {tun0}      |
                                       |              +-----------------------+
                                       |
                              +--------+-----------+
                              |                    |
                              |  Other LAN clients |
                              |                    |
                              |   192.168.0.0/24   |
                              |   (internal net)   |
                              +--------------------+