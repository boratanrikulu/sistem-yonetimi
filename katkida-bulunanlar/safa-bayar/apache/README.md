# APACHE


Kursun son gunlerinde doruk fişek hocamızın anlattığı web konusunu sizlere aktarmaya çalışacağım.

Doruk hoca konuyu slayt üzerinden anlattı. Sonrasında bazı görevler verip bunları yapmamızı istedi.  Kendi notlarım ve hocanın anlattıklarından aklımda kalanları anlatmayı planlıyorum. Herhangi bir konuda yanlış görürseniz lütfen bildirmeyi unutmayın.


+ Derste bir adet centos minimal sanal makine istenmektedir.
+ Anlatımda ki hataları değiştirmek için: [Gitlab](https://gitlab.com/rection/lyk18-SistemYonetimi-2-duzey.git)
+ Ders de kullanılan slayt için: [Slayt](http://topluluk.ozguryazilim.com.tr/wp-content/sunumlar/sistemyonetimi-sunumlar/apache_web_sunucusu.html#1)

## Web Mimarisi  

![Alt text](images/0.png)  

Fotoğrafda ki istemciler web tarayıcılarıdır. Web tarayıcıları, internette yayınlanmış bir sayfaya ulaşmak için bir arayüzdür. Bunlara örnek verir isek firefoxi chrome, safari gibi araçlardır. Tarayıcıdan herhangi adresi arattığınız zaman, ağlar aracılığıyla adresin bulunduğu sunucuya gidilmektedir. Sunucudan sayfa istenmesi için sunucu da web sunucusu olmalıdır.  

Web sunucusu, istenen sayfanın olup olmadığını kontrol edip istenen sayfayı vermektir. Eğer sayfa bulunamadıysa hata kodu dönmektedir. Eğer sayfa bulunursa, yapılmasını istenen farklı bir işlem varsa (kayıt, veri işleme...) uygulama sunucusuna yönlendirilir. Uygulama sunucusu, gelen isteğin içinde bulunan işlemleri yapması için yönlendiren ve kontrol eden mekanizmadır. Uygulama sunucusuna örnek verirsek zend(php), J2EE(java) gibi araçlardır. Eğer sistemde veritabanı kullanılmış ise uygulama sunucusu veritabanına istek gönderip işlenmesini sağlar. Veritabanını ile uygulama sunucusu arasında haberleşme http protokolü ile yapılır.  


HTTP: Hyper Text Transfer Protocol, 80. port kullanılmaktadır.  
HTTPS: HTTP Secure (Güvenli), 443. port kullanılmaktadır.  
URL: Bir web sunucusunun bulunduğu konumun, insanlar tarafından okunan kısmıdır.  

Netcraft'ın verdiği rakama göre (Eylül 2018) web sunucusu payları:  

![Alt Img](images/1.png)    


StatCounter'ın verdiği rakama göre (Ağustos 2018) istemci payları:  

![Alt Img](images/2.png)  


### Apache Tarihçesi

1991 - HTTP'nin 0.9 duyuruldu.  
1994 - NCSA HTTPd geliştirilmesi durdu.  
1995 - NCSA temelli "A Patchy Server" çıktı.  
1996 - Zamanla ismi Apache olup dünyanın en popüler sunucusu oldu.  
1999 - Apache Software Foundation kuruldu.  
2018 - 350+ Apache projesiyle Apache dünyanın en popüler sunucusudur.  

### Apache Kurulumu

Apache'nin kurulumu oldukça basittir. Centos 7 dağıtımı üzerinden anlatılmıştır. Debian dağıtımlarında komutlar ve konfigürasyon dosyalarının yeri değişmektedir.  

>`# yum install httpd `  

httpd paketini indirip, yüklemektedir.    

>`# service httpd start `  

Yükleme sonrasında servisi çalıştırıyoruz.  

>`# chkconfig httpd on `  

Her açılışta tekrardan başlatmamak için bu komutu uyguluyoruz.  

>`# sudo service firewalld start `  

Dışarıdan bağlantıya izin verilmesi için firewalld servisini başlatıyoruz.

>`# sudo firewall-cmd --permanent --add-port=80/tcp`  

Firewall üzerinden 80.portu tcp protokolü için açıyoruz.  


Ayar dosyaları ise şunlardır;  

`` >/etc/httpd/conf/httpd.conf ``  
`` >/etc/httpd/conf.d/*.conf ``
`` >/etc/sysconfig/httpd ``

### Çalışma Modelleri  

MPM (Multi-Processing Modules): Web sunucusunun temel işlevlerini değiştirmek için kullanılır. Bu apache'nin modüler dizaynı sayesinde olmaktadır. Bütün http işlemlerinde kullanılır. Bu aşamaları kullanmak için üç tane seçim aşaması vardır. Bunlar Prefork, Worker ve Event'tır.

>**Prefork (öntanımlı):** Apache ilk calıştırıldığında alt süreçlerle başlamaktadır. Bunlar öntanımlı olarak gelen istekleri karşılamak için çalışan servislerdir. Prefork bunun kontrol edilmesini sağlar.

>**Worker (Threaded):** Bir bağlantı olduğu zaman her alt işlemler yeni kuyruklar oluşturur. Prefork'tan farkı burada bağlantı olduğu zaman yeni bir alt işlem oluşturulur.

>**Event (Threaded - Apache 2.4+):** Diğer işlemlere göre daha fazla isteği aynı anda sunulmasına izin verir. Burada ilk isteği tamamladıktan sonra istemci bağlantıyı açık tutabilir. Bu sayede aynı soketi kullanarak daha fazla istek gönderebilir ve bağlantıya aşırı yüklenmesini azaltır.    

Nginx varsayılan olarak Worker(Threaded) çalışan bir servistir. Apache varsayılan olarak prefork çalışmaktadır. Her iki web sunucusunu da anlatılan aşamalardan birine geçirebiliriz.   

### Moduler Yapısı:
Birçok ek işlev httpd core'unun üzerine eklenir. Httpd core, moduler yapıyı yönetmeyi sağlamaktadır. Apache'nin en iyi özelliği modül biçiminde olmasıdır. Her defasında modüllerin okunması gerektiğinden dolayı, ne kadar az modül eklenirse o kadar performanslı çalışır. Modüller hem dinamik yüklenebilir, hem de statik olarak gömülebilmektedir. Statik olması avantaj olarak görülmektedir fakat herhangi bir değişiklik yapıldığı zaman, tekrardan derlenmesi gerekmektedir. Bu da sistemin yavaşlamasına sebep olmaktadır.

Statik derlenmiş modülleri görmek için **`# httpd -l `** komutu kullanılır.

#### Modüllerin Eklenmesi/Çıkarılması:

/etc/httpd/conf/httpd.conf dosyasının içinde 51. satırda `LoadModule auth_basic_module modules/mod_auth_basic.so` bulunmaktadır. 'modules', /etc/httpd/Modules dizinini belirtir. Eklemek istediğiniz modülü modules dizini içine koyulmalıdır.

Apache'nin kendi modüllerinden bazıları şunlardır:  

+ mod_dav: HTTP protokolü ile uzak sunucu da kaynakların oluşturulması, taşınması ve silinmesi gibi işlemlerin yapılmasını sağlar.  
+ mod_deflate: Sunucu tarafında sıkıştırılma işlemi yapılmasını sağlamaktadır.  
+ mod_ldap: Arka tarafta çalışan LDAP servisinin performansını geliştirmek için kullanılan modüldür.  

Apache'nin yaygın kullanılan ek modüllerden bazıları şunlardır:

+ mod_pagespeed: Otomatik olarak web optimizasyonu sağlar.
+ mod_security: Kendi için firewall sistemi vardır. Sql injection gibi güvenlik açıkları için de kullanılabilir.
+ mod_wsgi: Python dilinde yazılan uygulamanın ayarları ve sunumunda kullanılır.  

## Temel Ayarlar:  


/etc/httpd/conf/httpd.conf dosyasının 30. satırda **ServerRoot** ayarı bulunmaktadır. Yapılandırma dosyalarının nerede bulunduğunu belirtir.  

/etc/httpd/conf/httpd.conf dosyasında 109. satırda **DocumentRoot** ayarı bulunmaktadır. Sunulacak olan dosyanın yerine belirtir. Genellikle /var/www/html'dir.  

/etc/httpd/conf/httpd.conf dosyasında 88. satırda **ServerName** ayarı bulunmaktadır. Özel hostname ve port numarasının girildiği yerdir. Hostname olma zorunluluğu yoktur. Domain name servisine kayıtlı olan www.exapmle.com gibi adres de olabilir.


/etc/httpd/conf/httpd.conf dosyasında 40. satırda **Listen** ayarı bulunmaktadır. Hangi ip adresi ve port üzerinden sunum yapacağını belirtir. ipv6 desteklemektedir.  

Virtualhost tanımlarken kullanılan **ServerAlias** ise birden fazla domain name tanımlamaya yarar.  

## Dizinlere Özgü Ayarlar:

Öncelikle tek dizinlerin kullanımından bahsedeceğim.  

>```<Directory />```  
>```    Options FollowSymLinks```    
>```    AllowOverride None```    
>```</Directory>```  


**Options FollowSymLinks:** Varsayılan olarak gelmektedir. Sunucunun dizin içinde sembolik linkleri takip etmesini ifade eder. Daha fazla bilgi için [tıklayınız](https://httpd.apache.org/docs/2.4/mod/core.html#Options%20Directive).

**AllowOverride None:** Dizin sisteminde dosyaları okuyabilmesini reddetmesini ifade etmektedir. ***.htaccess*** dosyaları için önemlidir. Uygulanabilecek diğer metodlar kimlik doğrulaması(AuthConfig), Dosya bilgilendirmesi(FileInfo)... Ayrıntılara bakmak için [tıklayınız](https://httpd.apache.org/docs/2.4/mod/core.html#allowoverride).

Çoklu dizinler de kullanılması için şu şekilde ayarlanmalıdır.

>```<DirectoryMatch /[a-d].*>```  
>```    Options -FollowSymLinks```  
>```    AllowOverride All```  
>```</DirectoryMatch>```  

DirectoryMatch'da tanımlanan '/[a-d].*' kısımında  anlatılmak istenen a ve d arasında ki harfler ve sonrasında gelen dizin eşlemeleri için bu kuralları uygula demektir.  
**Options -FollowSymLinks** kısımında bulunan '-' bu özelliğin kapatılmasını ifade etmektedir. Varsayılan olarak + gelmektedir.    
**AllowOverride All** dosyaları okumasına izin verdiğini belirtmektedir.

Bunlara benzer şekilde ***dosya, url, proxy ve virtualhostlara*** özgü ayarlar bulunmaktadır. Bazı önemli ayarlar vardır.

**Order allow,deny:** ***İzin verme kurallarından*** önce ***Reddet kurallarınının*** işlenenceğini belirtmektedir. Eğer client ***İzin kurallarıyla*** eşleşme yapmazsa veya eşleşen ***Reddet kuralı*** var ise o zaman client erişimi reddeder.  

**Order deny,allow:** ***Reddet kurallarından*** önce ***İzin verme kurallarının*** işleneceğini belirtmektedir. Eğer client ***Reddet kuralıyla*** eşleşme yapmazsa veya eşleşen ***İzin kuralı*** var ise client erişime izin verir.  

Bu satırın altında Deny(Reddet) ve Allow(İzin verme) satırları yazılması zorunluluktur. Daha ayrıntılı bilgi için [tıklayınız](http://www.maxi-pedia.com/Order+allow+deny).  

## DirectoryIndex  
Dizinlere erişimlerin ayarlanması ve  hata kodlarının buna göre ayarlanmasını da sağlar. Tanımlı dosya eşleşmez ise Indexes özelliği inclenir. Indexes yetkisi varsa dosya listesi gösterilir. Indexes yetkisi yoksa "403 Forbidden" döndürür.  

**ServerTokens:** Sunucunun döndüğü cevabın başında sunucunun başında taşınan bilgilerdir. Apachenin ve İşletim sisteminin versiyonuna göre verilen bilgileri göstermektedir. Daha fazla bilgi için [tıklayınız](https://httpd.apache.org/docs/2.4/mod/core.html#servertokens).  

**StartServers 10:** Varsayılan olarak 10 tane süreç aç ve isteğin gelmesini bekle demektir. Daha fazla bilgi için [tıklayınız](https://httpd.apache.org/docs/2.4/mod/mpm_common.html#startservers).  

**MinSpareServers 5:** Boştaki alt süreçlerinin asgari sayısını belirler. Boştaki süreç, o an bir isteğe hizmet sunmayan süreçtir. Eğer MinSpareServers sayıda süreçten daha az boşta süreç varsa ana süreç sayıyı tamamlamak için yeni çocuk süreçler oluşturacaktır. Daha fazla bilgi edinmek için [tıklayınız](https://httpd.apache.org/docs/2.4/tr/mod/prefork.html).  

**MaxSpareServers 20:** Boştaki alt süreçlerinin azami sayısını belirler. Boştaki süreç, o an bir isteğe hizmet sunmayan süreçtir. Eğer MaxSpareServers sayıda süreçten daha fazla boşta süreç varsa ana süreç bu fazlalıkları öldürecektir. Daha fazla bilgi edinmek için [tıklayınız](https://httpd.apache.org/docs/2.4/tr/mod/prefork.html).  

**MaxRequestsPerChild 4000:** Kaç işlem sonrasında işlemin öldüreceğini belirtir. Daha fazla bilgi için [tıklayınız](https://httpd.apache.org/docs/2.4/mod/mpm_common.html#MaxRequestsPerChild).  

**MaxClients 150:** Ram miktarına göre maksimum clients ihtiyacına göre ayarlamaya yarar. Daha fazla bilgi için [tıklayınız](https://2bits.com/articles/tuning-the-apache-maxclients-parameter.html).  

**ServerLimit 256:** Apache işlemlerinin maksimum harcayabileceği ram miktarını belirler. MaxRequestWorkers ile orantılı bir şekilde ayarlanmalıdır. Daha fazla bilgi edinmek için [tıklayınız](https://httpd.apache.org/docs/current/en/mod/mpm_common.html).  

**KeepAlive On:** Arka arkaya istek gelmesine izin vermektedir. Daha fazla bilgi için [tıklayınız](https://httpd.apache.org/docs/2.4/mod/core.html#KeepAlive).  

**KeepAliveTimeout 15:** Sunucu kalıcı bir bağlantıyı kapatmadan önce, sonraki isteği kaç saniye bekleyeceğini belirtir. Kullanıcıların hızını bakılıp ayarlanması gerekmektedir. Daha fazla bilgi edinmek için [tıklayınız](https://httpd.apache.org/docs/2.4/tr/mod/core.html).  

**MaxKeepAliveRequests 4000:** Bağlantı başına izin verilecek istek sayısını sınırlar. Sunucu başarımını yüksek tutmak için yüksel bir değer olmalıdır. Daha fazla bilgi için [tıklayınzı](https://httpd.apache.org/docs/2.4/mod/core.html#maxkeepaliverequests).  

## .htaccess  
.htaccesss sunulacak olan verilerin bulunduğu dizine özgü ayarların yapılmasını sağlayan bir araçtır. Ana sunucu yapılandırma dosyasına erişilemediğinde veya değistirilemediğinde .htaccess dosyası kullanılmalıdır. Ayrıca eski siteden yeni siteye veya eski sayfalardan yeni sayfalara yönlendirmek için kullanılır.

+ httpd.conf içinde AccessFileName ayarı etkinleştirilmelidir.
>      AccessFileName .htaccess
+ httpd.conf'ta yapılan ayarlar dizin bazlı değiştirir.  
+ Apache öncelikle her dizinde .htaccess dosyasını arar.  
+ O dizin ve altındaki dizinler için bu ayarlar geçerli olur.  

#### Avantajlar:  
+ httpd.conf'u düzenleme yetkisi gerektirmez.    
+ Ayarlar anında etkili olur.  
+ Dizinin taşınmasında ayarlar da taşınır.  

#### Dezavantajlar:
+ Performansı düşürür. Bir belge istendiğinde her dizinin kök dizinine kadar daha yukarı bakmalıdır ve her dizinin değiştirilmesi aşamasında tekrar en başta ki .htaccess dosyasını okumaktadır. Bu da yavaşlamasına sebep olmaktadır.  
+ Güvenliği azaltır. Dosyalarındaki yönergelerin yanlış yapılandırması, dizindeki ve alt dizinlerin içindeki belgeler de sorunlara neden olabilir.

#### Alias
Dosyaların sunulacağı dizinin farklı bir yerde saklanmasını mümkün kılar. Url yolu ile dizin yolunun eşlenmesini sağlar.

#### Redirect
Eski bir url'i yönlendirmek için kullanılır. Https'e zorlamak için kullanılan bir yöntemdir. Redirect'de regex kullanılamaz iken *RedirectMatch* ile kullanılabilmektedir.  

>```Redirect 301 /eski_yeri.html http://www.ozguryazilim.com.tr/yeni_yeri.php```  
>```RedirectMatch ^/images/(.*)$ http://images.ozguryazilim.com.tr/$1```

*Rewrite konusunu hocamız slaytta yazılanları anlattı. Zaten slayt yeterince bu konu hakkında açıklayıcıdır.*  

## SSL ile Şifreleme:

Açılımı Secure Sockets Layer'dir. Amacı bir websitesi ile ziyaretçinin taraycısı arasında güvenli bağlantı oluşturmasını sağlamaktadır. Diğer deyişle karşı tarafın ulaşmak istediğimiz yer olduğunu doğrulamaktır. SSL domain name içindir. Sunucu için değildir. Daha fazla bilgi edinmek için [tıklayınız](http://www.networksolutions.com/education/what-is-an-ssl-certificate/).

SSL kurulum aşamasından bahsetmiyoruz çünkü dersin son kısımda verilen görevlerden birisidir.

Eğer dosya upload edilecekse öncelikle /tmp/ dizinin altına indirilmektedir. Sonrasında aktarılmaktadır. Bunların tanımlanması php sunulmasında kullanılır.

## Proxy:

Basit bir tanımla internet erişimi sırasında kullanılar ara sunucudur. Sadece tarayıcı üzerinden ayarlanabilmektedir. Bir bağlantıda öncelikle isteğiniz proxy sunucusuna sonrasında internete açılmaktadır. Kullanılmasının amacı yasaklı sitelere girilebilmesidir ama vpn gibi değildir. Proxy sunucusu ile aranızda ki bağlantı şifreli değildir ve proxy sunucusu https desteklemeyebilir. İki çeşidi vardır.

![proxy](images/3.png)

#### Forward Proxy(Yönlendirilmiş proxy):
Yukarıda anlatılan olan konu forward proxy'dir.

#### Reverse Proxy(Ters Proxy):
Forward proxy'nin tersidir. Client tarafından gelen istekleri karşılayıp arkada ki konumlandırılmış sunucuya yönlendirir.

İkisinin farkı: Forward proxy client tarafının kimliğini saklarken, Reverse proxy sunucunun kimliğini saklamasını sağlamaktadır.

Ters proxy ile sunucu tarafınta ssl yükünü kaldırabiliyoruz. Bu sayede sunucunun yükü azalmaktadır. Debug ve capture etmek için sunucu tarafında http kullanılmalıdır.

## Apache Logları

Varsayılan olarak log kayıtları /var/log/httpd/ altında tutulmaktadır. İsteğe göre client ismi ile tutulması mümkündür. Logların gün sonunda sıkıştırılıp imzalanması gerekmektedir. Loglar herhangi bir yasal durum karşısında sorun oluşmaması için 2 yıl saklanması gerekmektedir. Logların tutulmaması karşısında suçlu kayıt tutması gereken kişi olmaktadır. Ayrıntılı bilgi için [tıklayınız](http://www.mevzuat.gov.tr/MevzuatMetin/1.5.5651.pdf).

Logların analizinin yapılması için bazı araçlardan bahsedildi. Bunlardan en fazla piwik kullanılmaktadır.


#### Doruk Hoca'nın verdiği ödevler:  
1-)SSL  
2-)Reverse Proxy -----> hürriyet.com.tr'ye yönlendirmesi istenmektedir.  
3-)Virtualhost ------->test1.linux.gen.tr  
               |   
               ------->test2.linux.gen.tr  
4-)Parolalı alan oluşturulması istenmektedir.  
5-)PHP-fpm kurulumu -----> reverse proxy ----> phpinfo()  
6-)IP kurulumu

Sonraki günlerde ödevlerin sonuçları açıklanacaktır. ***Yapım Aşamasında***
