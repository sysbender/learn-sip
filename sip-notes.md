

## check out branch

```shell
git archive --remote=https://github.com/username/repository.git branch-name | tar -x -C /path/to/your/folder/


git archive --remote=git@github.com:keithcroxford/kamailio-course.git 01-basic-config | tar -x -C ./01-basic-config
```


```
+----------------------------------------------------------+
|                      kamailio (SIP Proxy)               |
|----------------------------------------------------------|
| Container: kamailio-edge                                 |
| Image: kamailio/kamailio-ci:5.4-alpine.debug             |
| Ports: 5090:5060 (SIP Port)                              |
| Volumes: ./kamailio-default/:/etc/kamailio               |
| Environment: KAM_SHM_MEM_SIZE=256                       |
| Networks: external (192.168.254.2) + internal (172.16.254.2) |
+----------------------------------------------------------+
      |  Routes SIP traffic to/from
      v
+---------------------------+          +---------------------------+
| b2bua_internal_01 (SIP Client) |    <-->| b2bua_internal_02 (SIP Client) |
|-----------------------------|          |-----------------------------|
| Container: b2bua_internal_01 |          | Container: b2bua_internal_02 |
| Image: andrius/pjsua:latest   |          | Image: andrius/pjsua:latest   |
| Command: pjsua --null-audio  |          | Command: pjsua --null-audio  |
| Ports: 5091:5060 (SIP Port)  |          | Ports: 5092:5060 (SIP Port)  |
| Networks: internal (172.16.254.100) |  | Networks: internal (172.16.254.101) |
+---------------------------+          +---------------------------+
      ^                                               ^
      |                                               |
      | Monitored by                                  | Monitored by
      v                                               v
+---------------------------+          +---------------------------+
|   sngrep (Packet Analyzer)  |    <-->| b2bua_external_01 (SIP Client) |
|-----------------------------|          |-----------------------------|
| Container: sngrep           |          | Container: b2bua_external_01 |
| Image: custom Dockerfile    |          | Image: andrius/pjsua:latest   |
| Ports: None                 |          | Command: pjsua --outbound     |
| Volumes: None               |          | sip:192.168.254.2;lr         |
| Networks: host (Network Mode)|          | Ports: 5093:5060 (SIP Port)  |
+---------------------------+          | Networks: external (192.168.254.100)|
                                        +---------------------------+
                                               |
                                               v
                                       +---------------------------+
                                       | b2bua_external_02 (SIP Client) |
                                       |-----------------------------|
                                       | Container: b2bua_external_02 |
                                       | Image: andrius/pjsua:latest   |
                                       | Command: pjsua --auto-answer=200|
                                       | Networks: external (192.168.254.101)|
                                       +---------------------------+


```