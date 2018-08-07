# Debian Linux Administration
How to Configure Mail Server SSL/TLS on Linux Debian

Basic Mail SSL 
1. #apt-get install postfix dovecot-imapd 
     Internet Site 
     System mail name: wsc17.cloud 
     Root and postmaster mail recipient: (blank) 
     Force synchronous updates on mail queue? <No> 
     Local networks: 172.16.0.0/16 10.0.0.0/8 
     Use procmail for local delivery? <No> 
     Mailbox size limit (bytes): 0 
     Local address extension character: + 
     Internet protocols to user: all 
2. #maildirmake.dovecot /etc/skel/Maildir 
3. #pico /etc/postfix/main.cf 
     home_mailbox = Maildir/ 
4. #/etc/init.d/posfix restart 
5. #adduser test1 
6. #adduser test2 
7. #pico /etc/dovecot/conf.d/10-mail.conf 
        #mail_location = mbox:~/mail:INBOX=/var/mail/%u 
         mail_location = maildir:~/Maildir 
8. #pico /etc/dovecot/conf.d/10-auth.conf 
# line 10: uncomment and change ( allow plain text auth ) 
disable_plaintext_auth = no 
# line 100: add 
auth_mechanisms = plain login 
#/etc/init.d/posfix restart 
9. #/etc/init.d/dovecot restart 
 
 
POSTFIX STARTLS 
smtpd_use_tls = yes 
smtpd_tls_key_file = /etc/postfix/smtpd.key 
smtpd_tls_cert_file = /etc/postfix/smtpd.cert 
 
/etc/init.d/postfix restart 
 
 
Enable SMTP submission (TLS tcp/587). 
 
1. pico /etc/postfix/master.cf 
 submission inet n - - - - smtpd 
   -o syslog_name=postfix/submission ..... 
   -o smtpd_tls_security_level... 
#smtpd_sasl_auth_enable=yes 
 
 
 
2. netstat -tulpn | grep 587 
 
 
POSTFIX TLS 465 and IMAPS 993 
 
1. cp wsc17cloud.cert.pem /etc/ssl/private/ 
2. cp wsc17cloud.key.pem /etc/ssl/private/ 
3. pico /etc/postfix/main.cf  
   Edit tls parameter 
   to /etc/ssl/private/wsc17cloud.cert.pem wsc17cloud.key.pem 
cukup seperti dibawah ini ini ada kaitannya sama diatas "POSTFIX STARTLS" 
4. pico /etc/postfix/master.cf 
smtps ... 
until 
wrappermode 
 
/etc/init.d/postfix restart 
 
install cacert.pem in the client 
 
5. pico /etc/dovecot 10-ssl.conf (port 993) 
 
ssl = yes 
ssl_cert = </etc/ssl/private/wsc17cloud.cert.pem 
ssl_key = </etc/ssl/private/wsc17cloud.key.pem 
 
6. /etc/init.d/dovecot restart 
7. openssl s_client -showcerts -connect localhost:993 -CAfile /ca/cacert.pem
