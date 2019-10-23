# Squid

Squid, cachleme işlemlerini ve HTTP bağlantılarını yönlendirmenizi sağlayan bir web proxy aracıdır. Çok geniş kullanım alanı vardır. Squid ile Ağı loglayabilir, kontrol edebilir, yönetebilir ve yönlendirebilir. Ayrıca aradaki bağlantının hızını kontrol edebilirsiniz. [Cache](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching) ile ilgili bilgi almak için linki takip edebilirsiniz.

Squid, SSL,TLS ve HTTPS protokollerini kullanabilmektedir fakat Privoxy'nin aksine SOCKS protokolünü desteklememektedir. Squid varsayılan olarak 3128 portunu kullanmaktadır, servis ayar dosyası içerisinden değiştirerek farklı portu kullanabilirsiniz.

# Kurulum

Centos 7 üzerinde kurulum yapmıştık ve bu kurulum sunucu tarafında yapılmalıdır.

```
# yum install squid
```

Squid'in ayar dosyaları */etc/squid* altında bulunmaktadır. Varsayılan olarak gelen ayar dosyaların amacı aşağıda belirtilmiştir.

* __/etc/squid/squid.conf :__ Genel squid ayarlarının bulunduğu dosya. Genellikle bu dosya kullanılacaktır. İp adresi ekleme, yasaklı web sitelerinin eklenmesi gibi işlemler için burası kullanılmaktadır.
* __/etc/squid/mime.conf :__ Bağlantıda kullanılabilecek ve görülebilecek dosya tiplerini belirtir. Varsayılan olarak HTTP/1.1'de kullanılan dosya tipleri vardır. Eğer dosya yasaklaması yapıyorsanız, bu sayfadan silmelisiniz.
* __/etc/squid/errorpage.css :__ Hata sayfasını düzenlemek ve değiştirmek için kullanılan dosyadır.
* __/etc/squid/cachemgr.conf :__ Eğer sunucu uzak bir yerde bulunuyor ise bu dosya içerisinde belirtilmelidir. Genellikle yönetici için kullanılır. Squid Cache Manager ayarları içinde kullanılmaktadır.
* __/usr/share/squid/errors/ :__ Altında mevcut olan hata sayfaları bulunmaktadır. Örneğin ERR_ACCESS_DENIED,ERR_DNS_FAIL...
* __/etc/firewalld/services/squid.xml :__ Firewall servisine takıldığı zaman gösterilecek hata sayfayı belirtmek için kullanılır. Derste yaptığımız uygulama aşağıdadır.

```
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>squid</short>
  <description>This option allows http proxying using squid. If you need to allow remote hosts to access the local squid proxy, enable this option. You need the squid package installed for this option to be useful.</description>
  <port protocol="tcp" port="3128"/>
</service>

```

Öncelikle firewalld servisini başlatmamız gerekmektedir.

```
systemctl start firewalld
```

Firewall'a takılmaması için firewall tarafından izin verilmelidir. Bu izinler için aşağıdaki komutları uygulamşız.

```
firewall-cmd --zone=public --add-service squid
firewall-cmd --reload
```

### Yasaklamalar

/etc/squid/squid.conf içinde bazı kısımlar gösterildi. Bunlardan birincisi;

```
acl localnet src 10.0.0.0/8     # RFC1918 possible internal network
acl localnet src 172.16.0.0/12  # RFC1918 possible internal network
acl localnet src 192.168.0.0/16 # RFC1918 possible internal network
acl localnet src fc00::/7       # RFC 4193 local private network range
acl localnet src fe80::/10      # RFC 4291 link-local (directly plugged) machines
acl yasak dstdomain www.google.com
```

'acl' ile izin verilen ip adreslerini belirtmekteyiz. Ayrıca yasaklı siteleri burada belirtebilmekteyiz. Bunun için aşağıdaki satırı da düzenlemeliyiz.

```
http_access deny yasak
```

Birden fazla sayfa yasaklamak isterseyebilirsiniz. Bunun için bir dosyaya yazılı olan linkleri, ayar dosyası içinde tanımlamanız gerekmektedir. Örnek vermek gerekirse,

```
vi /etc/squid/ban_domains.txt
```

Yasaklamak istediğimiz adresler şu adresler olsun. Vi'den kaydedip çıkınız.

```
www.youtube.com
www.facebook.com
```

Ayar dosyası içerisinden şu şekilde tanımlama yapmanız gerekmektedir.

```
acl ban_domains dstdomain "/etc/squid/ban_domains.txt" 
http_access deny ban_domains
```

Sonrasında servisi yeniden yüklediğimiz zaman liste aktif olacaktır.

```
systemctl reload squid
```

### IP aralığına veya IP'ye Erişim izni Verme

### Loglama ve Log Sınırları

### Squid Cache Sıkışması
