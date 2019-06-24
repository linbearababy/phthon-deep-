Python SMTP发送邮件

SMTP（Simple Mail Transfer Protocol）即简单邮件传输协议,它是一组用于由源地址到目的地址传送邮件的规则，由它来控制信件的中转方式。

python的smtplib提供了一种很方便的途径发送电子邮件。它对smtp协议进行了简单的封装。

Python创建 SMTP 对象语法如下：

import smtplib

smtpObj = smtplib.SMTP( [host [, port [, local_hostname]]] )

参数说明：

    host: SMTP 服务器主机。 你可以指定主机的ip地址或者域名如: runoob.com，这个是可选参数。
    port: 如果你提供了 host 参数, 你需要指定 SMTP 服务使用的端口号，一般情况下 SMTP 端口号为25。
    local_hostname: 如果 SMTP 在你的本机上，你只需要指定服务器地址为 localhost 即可。
    Python SMTP 对象使用 sendmail 方法发送邮件，语法如下：

    SMTP.sendmail(from_addr, to_addrs, msg[, mail_options, rcpt_options])
参数说明：

      from_addr: 邮件发送者地址。
      to_addrs: 字符串列表，邮件发送地址。
      msg: 发送消息
      这里要注意一下第三个参数，msg 是字符串，表示邮件。我们知道邮件一般由标题，发信人，收件人，邮件内容，附件等构成，发送邮件的时候，要注意 msg 的格式。这个格式就是 smtp 协议中定义的格式。


简单来说，smtplib库主要是用于负责和邮件服务器进行通讯，email库则主要用于规定编写邮件的头、主体、内容、附件等。

发邮件之前我们需要用Python登录smtp服务器，这样才能有发送权限，所以，我们需要去邮箱手动开启smtp服务，然后记住服务器授权码（授权码意思是，你可以不用我的网页邮箱或者邮箱app登录，你可以用邮箱账号+授权码在后台来获取邮箱服务器的内容）

