# Moving from ChanSIP to ChanPJSIP – Joshua Colp

ITSP

- peer, user, friend

pjsip - dialog, transaction

sorcery - api to configuration , backend (wizard) or configure file -> object(immutable)
bucket -

## pjsip configuration

- endpoint - configuration when talking to another SIP device
- AOR (address of record) - holds inbound registrations
- identify - indentifies incoming traffic based on criteria
- Auth - in/out bound auth credentials
- registration - outbound registration

```ini
# SIP user
[alice]
type=user
allow=!all,ulaw
context=users

# endpoint

[alice]
type=endpoint
allow=!all,ulaw
context=users

# SIP outbound Registration
[general]
register => 1000@test.com/test

# pjsip outbound registration
[sipstation]
type=registration
server_uri=sip:test.com
client_uri=sip:1000@test.com
contact_user=test


# sip user with authentication
[alice]
type=user
allow=!all,ulaw
context=users
secret=supersecret

# pjsip authentication
[5551212]
type=auth
auth_type=userpass
username=5551212
password=secret

# sip peer - call from asterisk to another sip entity
[alice]
type=peer
allow=!all,ulaw
host=1.1.1.1

# pjsip endpoint with contact

[alice]
type=endpoint
allow=!all,ulaw
aors=alice

[alice]
type=aor
contact=sip:1.1.1.1

```

## wizard

pjsip_wiard.conf

pjsip configuration wizard phone

```ini
[1000]
type = wizard
accepts_registrations = yes
accepts_auth = yes
inbound_auth/username = 1000
inbound_auth/password = secret
endpoint/allow = !all,ulaw
endpoint/context= users

```

pjsip configuration wizard ITSP

```ini
[my-itsp]
type=wizard
sends_auth = yes
sends_registrations = yes
remote_hosts = sip.my-itsp.net
outbound_auth/username = my_username
outbound_auth/password = my_password
endpoint/context = default
aor/qualify_frequency =  15

```

- pjsip rfc4733 for RTP DTMF , not rfc2833 (name in configuration is different)
- PJSIP user SIP URI instead of hostnames with ports, so it can carry transport type as well

## configuration templates

inherit the template as a general section

```ini
[template-name](!)
setting=value

[object](template-name)
setting2=value
```

## dialing

- SIP device

SIP/peer
SIP/peer/extension
SIP/extension@peer
SIP/host
SIP/username:password@host

- PJSIP device

PJSIP/endpoint
PJSIP/extension@endpoint
PJSIP/endpoint/SIP URI

always include endpoint
