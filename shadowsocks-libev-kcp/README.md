# Shadowsocks-libev + kcptun
### How To Use
```bash
docker run --name shadowsocks_kcp -d -P randyzhong/shadowsocks-libev-kcp
```
### Environment Variables
```
* SS_SERVER_PORT    - Shadowsocks Server PoCompressionrt
* KCP_PORT          - KCP Server Port
* SS_CONFIG         - Shadowsocks Server Config Options
* KCP_CONFIG        - KCP Server Config Options
```
### Default Shadowsocks-libev Config 
```
    ss-server
       -s 0.0.0.0           required to setup ss-server mode
       -p 5020              shadowsocks port number
       -k secret            password of shadowsocks server
       -m aes-256-cfb       encrypt method: aes-256-cfb
       -f /tmp/ss.pid       the file path to store pid
       -t 300               socket timeout in seconds
       -u                   enable udprelay mode
       -d 8.8.8.8           setup name servers for internal DNS resolver
       -d 8.8.4.4           setup name servers for internal DNS resolver
       --fast-open          enable TCP fast open
  
```
For details, please go to https://github.com/shadowsocks/shadowsocks-libev
### Default KCP Server Config 
```
server_linux_amd64 
    -t 127.0.0.1:5020     set local shadowsocks server as the target server
    -l ：5021              kcp server listen address    
    --key kcptun          kcp secret key
   --crypt salsa20        cryption: salsa20
   --mode fast            profiles: fast 
   --mtu 1350             set maximum transmission unit for UDP packets
   --sndwnd 256           set send window size(num of packets) (default: 1024)
   --rcvwnd 1024          set receive window size(num of packets) (default: 1024)
   --nocomp               disable compression
   --dscp 46              set DSCP(6bit)
```
For details, please go to https://github.com/xtaci/kcptun
