* ansible
** 整体规划
   1. 将每个 role 改写为函数形式
      - 解藕，为后期从 servers.genee.cn 获取数据做准备
   2. 停用 "lims2 up" 命令，使用 ansible 接管
      - ansible 可以使用 docker 模块建立容器
      - 可以将 lims2spec 中的信息添加到 ansible
   3. 服务数据放至 /opt 中

** 基础服务
   
   | 25% | 1. docker：替换旧 docker 服务，使用 docker－engine                                                                |
   | 25% | 2. nginx：将 location 列为单独文件，以 include 方式加载到 server 中                                               |
   | 25% | 3. mail：使用 postfix 替换 msmtp (确保端口不对外开放)，之前遇到了 msmtp 发信慢的情况，后改换过 postfix 提高性能。 |

   | 50% | 4. logrotate：依赖于安装了哪些服务，依赖于变量如何编写                      |
   | 50% | 5. users：暂时使用当前配置                                                  |
   | 50% | 6. ssh：暂时使用原有配置                                                    |
   | 50% | 7. python：使用宿主机的 python                                              |
   | 50% | 8. ntp：选择服务地址（使用 vpn 地址同步时间？）添加 ntp 地址为 ntp.genee.in |
   | 50% | 9. cron：（前提需要有 lims2）使用 shell 模块获得 cron                       |

** 基础 docker 服务

   | 75% | 1. reserv-server：涉及到在宿主机添加 nginx 配置，拆分 nginx 配置后较容易                                                        |
   | 75% | 2. 增加 cf 数据库                                                                                                               |
   | 75% | - EP 修改 mariadb 可以在 /var/lib/mysql 为空的情况下启动                                                                        |
   | 75% | - 添加一个专门用的初始化 role 来修改 mysql 数据库密码等其他无法重复执行且只                                                     |
   | 75% | 需要一次的操作(留待调查，可以使用 mysql role 替换)                                                                              |
   | 75% | 3. 添加 cf 用户：使用 shell 命令进行添加                                                                                        |
   | 75% | 4. 修改目录权限：涉及到 /home/disk /var/lib/php5 使用 file 即可修改                                                             |
   | 75% | 5. 补全 spinx 配置：通过lims2的get_sphinx.php获得新的 sphinx 配置, 将该配置与原有配置进行比对, 如果发生变化则覆盖, 再重新刷索引 |

   | 100% | 6. 各种 docker 服务：使用 docker 模块编写。并采用U盘或光盘传输镜像。 |

** 最终 app

   | 100% | 1. lims2：涉及到 nginx、php 配置，及如何启动 lims2，涉及到如何升级 lims2 |
   | 100% | 2. backup：备份情况如下                                                  |
   | 100% | - mysql 数据库数据，binlog 数据                                          |
   | 100% | - /home/disk (因为太大，不备份)                                          |
   | 100% | - nginx 配置(使用 ansible 自动生成)                                      |
   | 100% | - docker 服务配置(使用 ansible 自动生成)                                 |
   | 100% | - /var/lib/lims2/（直接由 lims2 代码库管理）                             |
