# Install Stenographer
```
sudo yum install stenographer
```
## Configure Stenographer
```
cd /etc/stenographer/
ls
vi config  

```

## Config file

```
{
  "Threads": [
    { "PacketsDirectory": "/data/steno/thread0/packets/"
    , "IndexDirectory": "/data/steno/thread0/index/"
    , "MaxDirectoryFiles": 30000
    , "DiskFreePercentage": 10
    }
  ]
  , "StenotypePath": "/usr/bin/stenotype"
  , "Interface": "enp2s0"
  , "Port": 1234
  , "Host": "127.0.0.1"
  , "Flags": []
  , "CertPath": "/etc/stenographer/certs"
}
```
## Certificates
```
sudo stenokeys.sh stenographer stenographer
ll /data/
sudo chown -R stenographer: /data/steno
sudo systemctl start stenographer  
sudo systemctl status stenographer  

```

Stop stenographer
`systemctl stop stenographer`

Ethtool
A tool fo modifying a network card. We want to turn of any kind of offloading or other things the card woudl normally do because we wan to allow our tools to captues them in a raw format, by allowing the NIC to interact with the packets before we see them is not best practice.
```
ethtool -K eth1 tso off gro off lro off gso off rx off tx off sg off rxvlan off txvlan off  
ethtool -N eth1 rx-flow-hash udp4 sdfn
ethtool -N eth1 rx-flow-hash udp6 sdfn
ethtool -C eth1 adaptive-rx off
ethtool -C eth1 rx-usecs 1000
ethtool -G eth1 rx 4096  
```
Now the we ran this manually lets make it into a scrip incase we had multiple

```
#!/bin/bash

for var in $@
do 
    echo "turning off offloading on $var  
    thtool -K $var tso off gro off lro off gso off rx off tx off sg off rxvlan off txvlan off  
    ethtool -N $var rx-flow-hash udp4 sdfn
    ethtool -N $var rx-flow-hash udp6 sdfn
    ethtool -C $var adaptive-rx off
    ethtool -C $var rx-usecs 1000
    ethtool -G $var rx 4096
done
exit 0  
```