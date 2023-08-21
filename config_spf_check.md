
# [Postfix] SPF (Sender Policy Framework) Check 

# Table of contents

- [SPF (Sender Policy Framework) check](#spf-sender-policy-framework-check)
  - [Install](#install)
  - [Config](#config)
  - [Restart](#restart)
  - [Result](#result)
  - [Reference](#reference)


## Install 
- Update 
```
sudo apt update
```
- Install 
```
sudo apt-get install postfix-policyd-spf-python
```
## Config

### Link with Postfix 
- /etc/postfix/master.cf
```
vim /etc/postfix/master.cf
```

```
policyd-spf  unix  -       n       n       -       0       spawn
    user=policyd-spf argv=/usr/bin/policyd-spf
```
- /etc/postfix/main.cf
```
vim /etc/postfix/main.cf
```

```
policyd-spf_time_limit = 3600
smtpd_recipient_restrictions =
    ...
    reject_unauth_destination,
    check_policy_service unix:private/policyd-spf,
    ...
```

## Policyd Conf
- Backup
```
mv /etc/postfix-policyd-spf-python/policyd-spf.conf /etc/postfix-policyd-spf-python/policyd-spf.conf.backup
```
- /etc/postfix-policyd-spf-python/policyd-spf.conf
```
vim /etc/postfix-policyd-spf-python/policyd-spf.conf
```
```
#  For a fully commented sample config file see policyd-spf.conf.commented
debugLevel = 4
TestOnly = 0
HELO_reject = Fail
Mail_From_reject = Fail
PermError_reject = False
TempError_Defer = False
skip_addresses = 127.0.0.0/8,::ffff:127.0.0.0/104,::1
Hide_Receiver = No
```
## Restart 
```
/etc/init.d/postfix restart
```

## Result 
- System Log
```
Aug 21 12:51:58 mail policyd-spf[169802]: spfcheck: pyspf result: "['Pass', 'sender SPF authorized', 'mailfrom']"
Aug 21 12:51:58 mail policyd-spf[169802]: Pass; identity=mailfrom; client-ip=209.85.219.173; helo=mail-yb1-f173.google.com; envelope-from=anthanh264@gmail.com; receiver=ah@dinhha.online
Aug 21 12:51:58 mail policyd-spf[169802]: not peruser
Aug 21 12:51:58 mail policyd-spf[169802]: Action: prepend: Text: Received-SPF: Pass (mailfrom) identity=mailfrom; client-ip=209.85.219.173; helo=mail-yb1-f173.google.com; envelope-from=anthanh264@gmail.com; receiver=ah@dinhha.online  Reject action: 550 5.7.23
Aug 21 12:51:58 mail policyd-spf[169802]: prepend Received-SPF: Pass (mailfrom) identity=mailfrom; client-ip=209.85.219.173; helo=mail-yb1-f173.google.com; envelope-from=anthanh264@gmail.com; receiver=ah@dinhha.online

```
- Source Mail
```
Received-SPF: Pass (mailfrom) identity=mailfrom; client-ip=209.85.219.173; helo=mail-yb1-f173.google.com; envelope-from=anthanh264@gmail.com; receiver=ah@dinhha.online
```


## Reference 

* [Enable SPF (Sender Policy Framework) checking](https://makeityourway.de/enabling-spf-sender-policy-framework-checking-on-postfix/)
* [Policyd-spf - policyd-spf python configuration parameters](https://manpages.debian.org/testing/postfix-policyd-spf-python/policyd-spf.conf.5.en.html)
