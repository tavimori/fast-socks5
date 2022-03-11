# MSOCKS5 Server

A SOCKS5 server that can spoof client's IP.

The clients connect to this socks server using the SOCKS5 protocol, then the server will make outwards TCP/UDP connection using the client's real IP.

 (somehow like a transparent proxy )

## Use case

We assume a client's device is connected to the internet through a gateway. 
And for some special network, the latency between the client's device to the gateway can be large, comparing to the latency between the gateway and the internet)

```
Client Device - - - - - - - - - - Gateway ---- Internet
                 (large delay)        (small delay)
```

Using such a proxy server can save at least 1 RTT for DNS looking up. And the proxy is transparent to the external services.


## Protocol

The protocol extend the SOCKS5 address format:

```
[1-byte type][optional variable-length client IP][variable-length host][2-byte port]
```

The following types are defined:

| type | client IP                 | host                   |
| ---- | ------------------------- | ---------------------- |
| 0x01 | none                      | 4-byte IPv4 address    |
| 0x03 | none                      | variable length string |
| 0x04 | none                      | 16-byte IPv6 address   |
| 0x11 | 4-byte IPv4 address       | 4-byte IPv4 address    |
| 0x13 | 4-byte IPv4 address       | variable length string |
| 0x43 | 16-byte IPv6 address      | variable length string |
| 0x44 | 16-byte IPv6 address      | 16-byte IPv6 address   |
| 0x53 | 20-byte IPv4 IPv6 address | variable length string |

When host is a variable length string, it starts with a 1-byte length, followed by up to 255-byte domain name. Notice that 


## Requirment

* The clients' IP should be publicly routable.
* The socks server should be run at the gateway and owns the reverse route for the clients' IP.

## Test with extra capability

```
sudo capsh --caps="cap_net_admin=eip" -- -c "cargo test"
```