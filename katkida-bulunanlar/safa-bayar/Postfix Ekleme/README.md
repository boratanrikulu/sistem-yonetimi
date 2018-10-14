# Virtual Host

Virtual host kavramı derste, virtual mail dizinene sahip olanlar, farklı domain adlarına sahip olanlar ve unix kullanıcısı olmayan durumu için anlatıldı. Ben de anladıklarımı ve notlarımı size aktarmaya çalışacağım.

Büyük sistemlerde çok fazla kullanıcı bulunmaktadır ve her biri için domain adı girilmiş olmalıdır. Bundan dolayı çok fazla kaynak ve zaman gerekmektedir. Bu sorunun önüne geçmek için virtual host kavramı kullanılmaktadır.

Virtual host kavramı ise postfix'te her alıcı adresi kendisine ait bir tane sanal mail kutusu(virtual mailbox) olmalıdır. Sanal mail kutusuna sahip bir alıcı adresi bir ev dizinine ihtiyacı yoktur. Ek olarak sanal domain tanımlama da kötü domain isimlerin yönlendirmek için de kullanılabilmektedir.

Çalışma mantığı ise postfix, alıcının sanal mail kutularına ararken, kullanıcının mail adresi, uid ve gid'lerini tablo üzerinde arayıp ayırmaktadır. Ayırma işlemini "/" karakterine göre yapmaktadır. Yani virtual host tanımlarken "/" a çok dikkat edilmelidir.


```
 1 /etc/postfix/main.cf:
 2     virtual_mailbox_domains = /etc/postfix/vhosts.txt    //example.com ...more domains... file declaration...
 3     virtual_mailbox_base = /var/mail/vhosts
 4     virtual_mailbox_maps = hash:/etc/postfix/vmailbox
 5     virtual_minimum_uid = 100
 6     virtual_uid_maps = static:5000
 7     virtual_gid_maps = static:5000
 8     virtual_alias_maps = hash:/etc/postfix/virtual
 9
10 /etc/postfix/vmailbox:
11     info@example.com    example.com/info
12     sales@example.com   example.com/sales/
13     # Comment out the entry below to implement a catch-all.
14     # @example.com      example.com/catchall
15     ...virtual mailboxes for more domains...
16
17 /etc/postfix/virtual:
18     postmaster@example.com postmaster
```

- Domainleri bir dosyaya yazıp otomatik olarak kullanıcı adı ve şifrelenmesini sağlanmaktadır. *"vhosts.txt"* dosyasının yazımı şu şekilde olmalıdır.

```
domain1.com
domain2.com
virtual.org
```

- Bu ayarlar yapıldıktan sonra **postmap vhosts.txt** komutu çalıştırılmasıyla domain isimleri şu şekilde değişmektedir.

```
joe@domain1.com domain1.com/joe/
joe@domain2.com domain2.com/joe/
john@virtual.org virtual.org/john/
```

 - **postmap /etc/postfix/virtual:** komutuN çalıştırılmasıyla virtual dosyası ayarlanmaktadır.
 - **postmap /etc/postfix/vmailbox:** çalıştırılmasıyla vmailbox dosyası ayarlanmaktadır.
 - **postfix reload:** komutun çalıştırılmasıyla main.cf dosyasında ki ayarlar güncellenmektedir.

Oluşturulan virtual domainlerin virtual mail kutuları */var/spool/vmail* dizinin altında oluşrulmaktadır. Eğer bu mail kutularının kullanıcı dizinin altında bulunması istenirse şu komutlar uygulanarak otomatik olarak mailler ev dizinin altında toplanması sağlanmaktadır.

Kaynaklar:  

[1](https://www.howtoforge.com/linux_postfix_virtual_hosting)  
[2](http://www.postfix.org/VIRTUAL_README.html)
