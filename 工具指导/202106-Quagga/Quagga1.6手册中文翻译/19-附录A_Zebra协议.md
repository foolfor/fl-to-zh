# 附录 A Zebra 协议

## A.1 Zebra协议概述



## A.2 Zebra协议定义

### A.2.1 Zebra协议头（版本0）

```shell
0                   1                   2                   3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-------------------------------+---------------+
|           Length (2)          |   Command (1) |
+-------------------------------+---------------+
```

### A.2.2 Zebra协议头（版本1）

```shell
0                   1                   2                   3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-------------------------------+---------------+-------------+
|           Length (2)          |   Marker (1)  | Version (1) |
+-------------------------------+---------------+-------------+
|          Command (2)          |
+-------------------------------+
```

### A.2.3 Zebra 协议头域定义

```shell
'Length'
```

包括这个头的总包长度。版本 0 消息的最小长度为 3 个字节，版本 1 消息的最小长度为 6 个字节。

```shell
'Marker'
```

值始终为 255 的静态标记。这是为了允许版本 0 Zserv 标头（不明确包含版本）与版本化标头区分开来。版本 0 消息中不存在。

```shell
'Version'
```

Zserv 消息的版本号。对于他们不识别的版本，客户端不应继续处理超过版本字段的消息。版本 0 消息中不存在。

```shell
'Command'
```

Zebra 协议命令。

### A.2.4 Zebra协议命令

|              Command              | Value |
| :-------------------------------: | :---: |
|        ZEBRA_INTERFACE_ADD        |   1   |
|      ZEBRA_INTERFACE_DELETE       |   2   |
|    ZEBRA_INTERFACE_ADDRESS_ADD    |   3   |
|  ZEBRA_INTERFACE_ADDRESS_DELETE   |   4   |
|        ZEBRA_INTERFACE_UP         |   5   |
|       ZEBRA_INTERFACE_DOWN        |   6   |
|       ZEBRA_IPV4_ROUTE_ADD        |   7   |
|      ZEBRA_IPV4_ROUTE_DELETE      |   8   |
|       ZEBRA_IPV6_ROUTE_ADD        |   9   |
|      ZEBRA_IPV6_ROUTE_DELETE      |  10   |
|      ZEBRA_REDISTRIBUTE_ADD       |  11   |
|     ZEBRA_REDISTRIBUTE_DELETE     |  12   |
|  ZEBRA_REDISTRIBUTE_DEFAULT_ADD   |  13   |
| ZEBRA_REDISTRIBUTE_DEFAULT_DELETE |  14   |
|     ZEBRA_IPV4_NEXTHOP_LOOKUP     |  15   |
|     ZEBRA_IPV6_NEXTHOP_LOOKUP     |  16   |