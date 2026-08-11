# 5. ソケット

自作プロトコルスタックの移植が終わったので、次は「ソケット」を実装してアプリケーションがプロトコルスタックの機能を利用して通信できるようにします。

micropsにはソケット層（`sock.c`/`sock.h`）が用意されており、独自のディスクリプタ表でUDP/TCPのPCBを管理します。この章では、micropsのソケット層をxv6のファイルディスクリプタと統合し、`socket()`・`bind()`・`recvfrom()`・`sendto()`といった標準的なソケットAPIをシステムコールとしてユーザ空間に提供します。

```
ユーザ空間 (user/udpecho.c)
    ↓ socket(), bind(), recvfrom(), sendto()
システムコール (kernel/syssocket.c)   ← 新規作成
    ↓ struct file (FD_SOCKET) 経由で sock ディスクリプタを保持
ソケット層 (kernel/net/sock.c)        ← micropsからコピー
    ↓ udp_cmd_*()
プロトコルスタック
```

> [!NOTE]
> この章ではUDP（データグラム）ソケットを扱えるところまでを実装します。TCP（ストリーム）ソケットへの対応は応用課題として扱います。
