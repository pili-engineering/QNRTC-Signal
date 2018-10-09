# RTC 信令协议

## 基本格式

```
type={json-data}

信令是以 websocket 连接建立为基础的，通过自定义的消息格式来通信。
一个完整的消息包含三个部分，type为消息类型，=为分隔符号，{json-data}为json结构的消息内容。
形如"xxx"表示字符串类型，形如xxx表示数字类型，形如true/false表示布尔类型，形如{}表示对象类型，形如[]表示数组类型。
```

## 自定义心跳

```
ping={}
pong={}

客户端在收到ping消息时需主动回复pong消息，注意这里的ping/pong为信令层自定义的消息，服务端通过该协议来判断连接状态。
```

## 请求响应类消息

### 进入房间

```
格式
auth={"rpcid":"xxx","token":"xxx","reconntoken":xxx,"playerdata":"xxx","agent":"xxx","sdkversion":"xxx","capsdp":"xxx"}
// rpcid       消息id
// token       用户鉴权信息
// reconntoken 断线重连id
// playerdata  用户自定义附加信息
// agent       客户端agent
// sdkversion  客户端sdk版本
// capsdp      客户端sdp

auth-res={"rpcid":"xxx ","code":xxx,"error":"xxx","reconntoken":xxx,"players":[],"tracks":[],"my-pubs":[],"my-subs":[],"rtpcaps":{}}
// rpcid       消息id
// code        错误码
// error       错误描述
// reconntoken 断线重连id，供用户断线重连使用
// players     房间内用户列表
// tracks      房间内流列表
// my-pubs     自己发布的流列表，只在重连时才有
// my-subs     自己订阅的流列表，只在重连时才有
// rtpcaps     房间支持的rtc能力描述，具体格式见示例

player格式
{"playerid":"xxx","playerdata":"xxx"}
// playerid    用户id
// playerid    用户自定义附加信息

track格式
{"trackid":"xxx","playerid":"xxx","kind":"xxx","tag":"xxx","master":true/false,"muted":true/false}
// trackid     流的唯一id
// playerid    流所属用户id
// kind        流的类型，aduio或video
// tag         流的标签，用户自定义
// master      流的主属性，每一种类型只可以指定一个为主
// muted       音频静音或视频黑屏

my-pub格式
{"trackid":"xxx"}
// trackid     流的唯一id

my-sub格式
{"trackid":"xxx"}
// trackid     流的唯一id

示例
auth={"rpcid":"1538984630954","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...","reconntoken":0,"agent":" Chrome/69.0.3497.100","sdkversion":"1.0","capsdp":"v=0\r\no=- 9081281139296299718 2 IN IP4 127.0.0.1 ..."}

auth-res={"rpcid":"1538984630954","code":0,"error":"","reconntoken":370926,"players":[{"playerid":"yj","playerdata":""}],"tracks":null,"my-pubs":null,"my-subs":null,"rtpcaps":{"codecs":[{"name":"opus","mimeType":"audio/opus","kind":"audio","clockRate":48000,"channels":2,"sendPayloadType":111,"recvPayloadType":100,"rtcpFeedback":[],"parameters":{"minptime":10,"stereo":1,"sprop-stereo":1,"useinbandfec":1}},{"name":"H264","mimeType":"video/H264","kind":"video","clockRate":90000,"sendPayloadType":100,"sendRtxPayloadType":101,"recvPayloadType":101,"recvRtxPayloadType":102,"rtcpFeedback":[{"type":"goog-remb"},{"type":"ccm","parameter":"fir"},{"type":"nack"},{"type":"nack","parameter":"pli"}],"parameters":{"packetization-mode":1,"profile-level-id":"42e01f","level-asymmetry-allowed":1}}],"headerExtensions":[{"kind":"audio","uri":"urn:ietf:params:rtp-hdrext:ssrc-audio-level","sendId":1,"recvId":1},{"kind":"video","uri":"urn:ietf:params:rtp-hdrext:toffset","sendId":2,"recvId":2},{"kind":"video","uri":"http://www.webrtc.org/experiments/rtp-hdrext/abs-send-time","sendId":3,"recvId":3},{"kind":"video","uri":"urn:3gpp:video-orientation","sendId":4,"recvId":4}],"fecMechanisms":[]}}
```

### 创建上行连接
```
格式
pubpc={"rpcid":"xxx","sdp":"xxx"}
// rpcid           消息id
// sdp             offer sdp

pubpc-res={"rpcid":"xxx","code":xxx,"error":"xxx","pcid":"xxx","iceParameters":{},"iceCandidate":{},"dtlsParameters":{}}"
// rpcid           消息id
// code            错误码
// error           错误描述
// pcid            连接id
// iceParameters   ice信息，具体格式见示例
// iceCandidate    candidate信息，具体格式见示例
// dtlsParameters  dtls信息，具体格式见示例

示例
pubpc={"rpcid":"1538987466812","sdp":"v=0\r\no=- 2081837499070747556 2 IN ..."}

pubpc-res={"rpcid":"1538987466812","code":0,"error":"","pcid":"xvPsi7p0_yTn8ppm","iceParameters":{"usernameFragment":"onmcxfhb6r09osob","password":"9kv5qwikjo1q1thsih8a3h310p6vuayx","iceLite":true},"iceCandidate":{"foundation":"udpcandidate","priority":1078862079,"ip":"192.168.199.109","protocol":"udp","port":16560,"type":"host"},"dtlsParameters":{"role":"client","fingerprints":[{"algorithm":"sha-256","value":"6F:02:D2:E9:3C:2E:93:0F:A9:03:6F:14:1F:0B:E0:5A:EC:9A:C2:D2:DE:5B:90:30:DE:3E:32:78:F9:BA:5A:C2"}]}}
```

### 创建下行连接

```
格式
subpc={"rpcid":"xxx"}
// rpcid           消息id

subpc-res={"rpcid":"xxx","code":xxx,"error":"xxx","pcid":"xxx","iceParameters":{},"iceCandidate":{},"dtlsParameters":{}}"
// rpcid           消息id
// code            错误码
// error           错误描述
// pcid            连接id
// iceParameters   ice信息，具体格式见示例
// iceCandidate    candidate信息，具体格式见示例
// dtlsParameters  dtls信息，具体格式见示例

示例
subpc={"rpcid":"1538987790527"}

subpc-res={"rpcid":"1538987790527","code":0,"error":"","pcid":"x_NUEbV0h0L7_4Xa","iceParameters":{"usernameFragment":"65qvsjjvq4nawxzp","password":"fen0x8wsbhcn1ywyongx6tdp4exk2rtv","iceLite":true},"iceCandidate":{"foundation":"udpcandidate","priority":1078862079,"ip":"192.168.199.109","protocol":"udp","port":16514,"type":"host"},"dtlsParameters":{"role":"server","fingerprints":[{"algorithm":"sha-256","value":"6F:02:D2:E9:3C:2E:93:0F:A9:03:6F:14:1F:0B:E0:5A:EC:9A:C2:D2:DE:5B:90:30:DE:3E:32:78:F9:BA:5A:C2"}]}}"
```

### 销毁上行连接
```
格式
pubpc-close={"rpcid":"xxx","pcid","xxx"}
pubpc-close-res={"rpcid":"xxx","pcid","xxx"}
// rpcid       消息id
// pcid        连接id

示例
pubpc-close={"rpcid":"1538991723108","pcid":"xvPsi7p0_yTn8ppm"}

pubpc-close-res={"rpcid":"1538991723108","pcid":"xvPsi7p0_yTn8ppm"}
```

### 销毁下行连接

```
格式
subpc-close={"rpcid":"xxx","pcid":"xxx"}
subpc-close-res={"rpcid":"xxx","pcid","xxx"}
// rpcid       消息id
// pcid        连接id

示例
subpc-close={"rpcid":"1538991723961","pcid":"x_NUEbV0h0L7_4Xa"}

subpc-close-res={"rpcid":"1538991723961","pcid":"x_NUEbV0h0L7_4Xa"}
```

### 发布流
```
格式
pub-tracks={"rpcid":"xxx","tracks":[],"sdp":"xxx"}
// rpcid       消息id
// tracks      流列表
// sdp         offer sdp

track格式
{"localid":"xxx","kind":"xxx","tag":"xxx","master":true/false,"kbps":xxx},
// localid     流在端上的id
// kind        流的类型，aduio或video
// tag         流的标签，用户自定义
// master      流的主属性，每一种类型只可以指定一个为主
// kbps        流的码率限制，单位kbps，视频默认600，音频默认64

pub-tracks-res={"rpcid":"xxx","code":xxx,"error":"xxx","tracks":[]}
// rpcid       消息id
// code        错误码
// error       错误描述
// tracks      流列表

track格式
{"localid":"xxx","trackid":"xxx","status":true/false},
// localid     流在端上的id
// trackid     流的唯一id
// status      该流是否发布成功

示例
pub-tracks={"rpcid":"1538992159600","tracks":[{"localid":"e5863ae3-f37b-4815-9539-a75e0b3d00d0","kind":"audio","master":true},{"localid":"df19afb2-f6f2-4146-97df-657288f15567","kind":"video","master":true}],"sdp":"v=0\r\no=- 2868168464068663307 3 IN IP4 127.0.0.1 ..."}

pub-tracks-res={"rpcid":"1538992159600","code":0,"error":"","tracks":[{"trackid":"z_NB1higG2UYK3Ic","localid":"e5863ae3-f37b-4815-9539-a75e0b3d00d0","status":true},{"trackid":"0PNdxR5GR-_D3K_H","localid":"df19afb2-f6f2-4146-97df-657288f15567","status":true}]}
```

### 取消发布流
```
格式
unpub-tracks={"rpcid":"xxx","tracks":[]}
unpub-tracks-res={"rpcid":"xxx","tracks":[]}
// rpcid       消息id
// tracks      流列表

track格式
{"trackid":"xxx"}
// trackid     流的唯一id

示例
unpub-tracks={"rpcid":"1538992731031","tracks":[{"trackid":"z_NB1higG2UYK3Ic"},{"trackid":"0PNdxR5GR-_D3K_H"}]}

unpub-tracks-res={"rpcid":"1538992731031","tracks":[{"trackid":"z_NB1higG2UYK3Ic"},{"trackid":"0PNdxR5GR-_D3K_H"}]}
```

### 订阅流
```
格式
sub-tracks={"rpcid":"xxx","tracks":[]}
// rpcid       消息id
// tracks      流列表

track格式
{"trackid":"xxx"}
// trackid     流的唯一id

sub-tracks-res={"rpcid":"xxx","code":xxx,"error":"xxx","tracks":[]}
// rpcid       消息id
// code        错误码
// error       错误描述
// tracks      流列表

track格式
{"trackid":"xxx","status":true/false,"rtpparams":{}}
// trackid     流的唯一id
// status      该流是否订阅成功
// rtpparams   订阅流相关信息，具体格式见示例

示例
sub-tracks={"rpcid":"1538992810571","tracks":[{"trackid":"1fMK_cOyMvwkoDdS"},{"trackid":"1vOuo05K99xB0DKg"}]}

sub-tracks-res={"rpcid":"1538992810571","code":0,"error":"","tracks":[{"trackid":"1fMK_cOyMvwkoDdS","status":true,"rtpparams":{"encodings":[{"ssrc":42563057,"rtx":{"ssrc":0}}],"rtcp":{"cname":"H9V/CBXyAiwjASP+","reducedSize":true,"mux":true}}},{"trackid":"1vOuo05K99xB0DKg","status":true,"rtpparams":{"encodings":[{"ssrc":46184420,"rtx":{"ssrc":85391501}}],"rtcp":{"cname":"H9V/CBXyAiwjASP+","reducedSize":true,"mux":true}}}]}
```

### 取消订阅流
```
格式
unsub-tracks={"rpcid":"xxx","tracks":[]}
unsub-tracks-res={"rpcid":"xxx","tracks":[]}
// rpcid       消息id
// tracks      流列表

track格式
{"trackid":"xxx"}
// trackid     流的唯一id

示例
unsub-tracks={"rpcid":"1538992829077","tracks":[{"trackid":"1fMK_cOyMvwkoDdS"},{"trackid":"1vOuo05K99xB0DKg"}]}

unsub-tracks-res={"rpcid":"1538992829077","tracks":[{"trackid":"1fMK_cOyMvwkoDdS"},{"trackid":"1vOuo05K99xB0DKg"}]}
```

### 执行指定命令
```
格式
control={"rpcid":"xxx","command":"xxx","playerid":"xxx"}
// rpcid       消息id
// command     指令名称，当前支持kickplayer
// playerid    指令作用对象id

control-res={"rpcid":"xxx","code":xxx,"error":"xxx","command":"xxx","playerid":"xxx"}
// rpcid       消息id
// code        错误码
// error       错误描述
// command     指令名称，当前支持kickplayer
// playerid    指令作用对象id

示例
control={"rpcid":"1538991723962","command":"kickplayer","playerid":"test"}

control-res={"rpcid":"1538991723962","code":0,"error":"","command":"kickplayer","playerid":"test"}
```

### 创建合流任务
```
格式
create-merge-job ={"rpcid":"xxx","id":"xxx","publishUrl":"xxx","audioOnly":true/false,"height":xxx,"width":xxx,"fps":xxx,"kbps":xxx,"minRate":xxx,"maxRate":xxx,"stretchMode":"xxx","watermarks":[]}
// rpcid       消息id
// id          合流任务id
// publishUrl  合流推流地址
// audioOnly   是否只合成音频流
// height      合流输出高度
// width       合流输出宽度
// fps         合流输出帧率
// kbps        合流输出平均码率
// minRate     合流输出最小码率
// maxRate     合流输出最大码率
// stretchMode 画面填充方式，aspectFill或aspectFit或scaleToFit
// watermarks  水印配置，具体格式见示例

create-merge-job-res={"rpcid":"xxx","code":xxx,"error":"xxx","id":"xxx",}
// rpcid       消息id
// id          合流任务id
// code        错误码
// error       错误描述

示例
create-merge-job={"rpcid":"1538992810573","code":0,"error":"","id":"test","publishUrl":"rtmp://127.0.0.1/test/test","height":720,"width":1080,"fps":25,"kbps":1000,"audioOnly":false,"stretchMode":"aspectFill","watermarks":[{"w":40,"h":40,"x":400,"y":40,"file":"http://www.baidu.com/xxx.png"}]]}

create-merge-job-res={"rpcid":"1538992810573","id":"test"}
```

## 通知类消息（服务端通知客户端）

### 用户进入房间
```
格式
on-player-in={"playerid":"xxx","playerdata":"xxx"}
// playerid    用户id
// playerdata  用户自定义附加信息

示例
on-player-in={"playerid":"test","playerdata":"just for test"}
```

### 用户离开房间
```
格式
on-player-out={"playerid":"xxx"}
// playerid    用户id

示例
on-player-out={"playerid":"test"}
```


### 房间内增加流
```
格式
on-add-tracks={"tracks":[]}
// tracks      流列表

track格式
{"trackid":"xxx","playerid":"xxx","kind":"xxx","tag":"xxx","master":true/false}
// trackid     流的唯一id
// playerid    流所属用户id
// kind        流的类型，aduio或video
// tag         流的标签，用户自定义
// master      流的主属性，每一种类型只可以指定一个为主

示例
on-add-tracks={"tracks":[{"trackid":"yPNt7tpJD2Zv6dyn","playerid":"test","kind":"audio","tag":"aaaa","master":true},{"trackid":"yfPNe3ZpvUMKwa1G","playerid":"test","kind":"video","tag":"bbb","master":true}]}
```

### 房间内删除流
```
格式
on-remove-tracks={"tracks":[]}
// tracks      流列表

track格式
{"trackid":"xxx"}
// trackid     流的唯一id

示例
on-remove-tracks={"tracks":[{"trackid":"yPNt7tpJD2Zv6dyn"},{"trackid":"yfPNe3ZpvUMKwa1G"}]}
```

### 上行连接连通
```
格式
on-pubpc-connected={"pcid":"xxx"}
// pcid        连接id

示例
on-pubpc-connected={"pcid":"xvPsi7p0_yTn8ppm"}
```

### 上行连接断开
```
格式
on-pubpc-disconnected={"pcid":"xxx"}
// pcid        连接id

示例
on-pubpc-disconnected={"pcid":"xvPsi7p0_yTn8ppm"}
```

### 下行连接连通
```
格式
on-subpc-connected={"pcid":"xxx"}
// pcid        连接id

示例
on-subpc-connected={"pcid":"x_NUEbV0h0L7_4Xa"}
```

### 下行连接断开
```
格式
on-subpc-disconnected={"pcid":"xxx"}
// pcid        连接id

示例
on-subpc-disconnected={"pcid":"x_NUEbV0h0L7_4Xa"}
```

### 被动退出房间
```
格式
disconnect={"code":xxx,"error":"xxx","kickedid":"xxx",}
// code        错误码
// error       错误描述
// kickedid    踢人用户id，只有在被别人主动剔除房间时才存在

示例
disconnect={"code":10006,"error":"kicked out of the room","kickedid":"test"}
```

### 音频静音或视频黑屏
```
格式
mute-tracks={{"tracks":[]}}
// tracks      流列表

track格式
{"trackid":"xxx","muted":true/false}
// trackid     流的唯一id
// muted       音频静音或视频黑屏

示例
mute-tracks={"tracks":[{"trackid":"yPNt7tpJD2Zv6dyn","muted":true},{"trackid":"yfPNe3ZpvUMKwa1G","muted":true}]}
```

## 通知类消息（客户端通知服务端）

### 主动退出房间
```
格式
disconnect={}

示例
disconnect={}
```

### 停止合流任务
```
格式
stop-merge={"id":"xxx"}
// id          合流任务id，id为空表示默认合流任务

示例
stop-merge={"id":"test"}
```

### 更新合流配置
```
格式
update-merge-tracks={"id":"xxx","add":[],"remove":[]}
// id          合流任务id，id为空表示默认合流任务
// add         增加合流流列表，具体格式见示例
// remove      删除合流流列表，具体格式见示例

示例
update-merge-tracks={"id":"test","add":[{"trackid":"yPNt7tpJD2Zv6dyn"},{"trackid":"yfPNe3ZpvUMKwa1G","x":10,"y":10,"w":100,"h":100,"z":0}],"remove":[{"trackid":"yPNt7tpJD2Zv6dy_"},{"trackid":"yfPNe3ZpvUMKwa1_"}]}
```

### 音频静音或视频黑屏
```
格式
mute-tracks={{"tracks":[]}}
// tracks      流列表

track格式
{"trackid":"xxx","muted":true/false}
// trackid     流的唯一id
// muted       音频静音或视频黑屏

示例
mute-tracks={"tracks":[{"trackid":"yPNt7tpJD2Zv6dyn","muted":true},{"trackid":"yfPNe3ZpvUMKwa1G","muted":true}]}