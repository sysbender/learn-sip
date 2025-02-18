## carrier firewall

udp signal 5060
udp media 10000~20000

###

sip peer : extention and carrier

sip.conf

```ini
; ### Extensions ####
[101]  ; extension number
type=friend
secret=asterisk
disallow=all
allow=ulaw
host=dynamic
context=mycontext
qualify=yes
mailbox=101@internal-vm

[102]
type=friend
secret=asterisk
disallow=all
allow=ulaw
host=dynamic
context=mycontext
qualify=yes
mailbox=102@internal-vm
```

Parameter Purpose
[101] Defines the SIP peer for extension 101.
type=friend Allows both incoming and outgoing calls.
secret=asterisk Password for SIP authentication.
disallow=all Blocks all codecs by default.
allow=ulaw Allows G.711 u-law codec for calls.
host=dynamic Allows the SIP device to register dynamically.
context=mycontext Defines the call routing rules in extensions.conf.
qualify=yes Monitors the device’s connection status.
mailbox=101@internal-vm Assigns a voicemail box to the user.

```shell
sudo asterisk -rvvv
core set verbose 25
sip show peers
sip reload  # reload sip.conf
reload # reload all
sip show peers

core show settings
```

in default context, it can dial 600 echo test.

### extension

```ini
[internal]   # context - group extension and
exten => 1,1,Answer  # extension, priority, app
exten => 1,n,NoOp(This-is-a-sample-context)  # NoOp - debug message
exten => 1,n,Playback(vm-goodbye)
exten => 1,n,Hangup()
```

### create voice mail box

voicemail.conf

```ini
[internal-vm]
101 => 123456,Sys Bender,sysbender@gmail.com
102 => 123456,Sys Bender,sysbender@gmail.com
```

```ini
exten => 102,1,Answer  # answer when 102 is dial
exten => 102,n,NoOp(internal-phone-call)  # log
exten => 102,n,Dial(SIP/102,12,r)   # dial endpoint 102, timeout 12s, ring back to caller
exten => 102,n,Voicemail(102@internal-vm) # redirect to voicebox for ext 102
exten => 102,n,Hangup() # hang up
```

### dynamic dial plan with variable

```ini
exten => _1XX,1,Answer
exten => _1XX,n,NoOp(internal-phone-call)
exten => _1XX,n,Dial(SIP/${EXTEN},12,r)  # ${EXTEN} refers to the current extension being dialed
exten => _1XX,n,Voicemail(${EXTEN}@internal-vm)
exten => _1XX,n,Hangup()
```
