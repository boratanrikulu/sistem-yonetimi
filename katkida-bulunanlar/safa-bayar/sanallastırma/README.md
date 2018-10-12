#Virtualization (Sanallaştırma)

Mesutcan Kurt hocamızın anlattığı sanallaştırma konusunu aklımda kalanlarla ve tuttuğum notlarla  aktarmaya çalışacağım.  


+ Derste centos veya debian dağıtımı sanal makine istenmektedir. Sınıfta genel olarak debian dağıtımı tercih edildiği için konu debian üzerinden anlatılmıştır.
+ Anlatımda ki hataları düzeltmek için: [gitlab](https://gitlab.com/rection)


Sanallaştırma, fiziksel bir makinadan sanal kaynakların ayrılıp verimli halde kullanmasını sağlamaktadır. Sanallaştırma bilgisayar donanımları, depolama cihazları ve bilgisayar ağları üzerinde yapılabilmektedir. Derste anlatılanlar disk, ağ ve *donanım* sanallaştırma olduğu için sadece bu konulardan bahsedeceğim.

##Ağ Sanallaştırma:

Ağ sanallaştırması, bir sunucuda ki ağı, bir çok alt ağa veya sanal bölümlere ayrılmasıdır. Genellikle yazılım testlerinde kullanılmaktadır. Yazılımın çalışması amaçlandığı ağ ortamlarının simülasyonunda kullanılmaktadır. Simülasyonun sunduğu seçenekler ise ağ kartı (switch ve ağ cihazı), firewall, load balancer, virtual LAN(sanal makinalarda kullanılması için), ağ depolama aygıtları, haberleşme aygıtlarının simülasyonun da kullanılabilmektedir.

##Disk Sanallaştırma:

Blok halinde veya dosya halinde sanallaştırma yapılmasını sağlamaktadır.  

*Blok sanallaştırma*, diskte kurulmuş ama işlenmemiş bölümlerin kullanılması ve her bloğun bir birey gibi kontrol edilmesidir. Bu blokların kontrolü işletim sistemi tarafından yapılmaktadır. Her blok işletim sisteminin kendi dosya formatını kullanmaktadır.  

*Dosya sanallaştırma*, bir hard disk, NAS gibi depolama sistemleri için kullanılır. NFS veya SMB gibi protokoller kullanır. Depolama alanları sonrasında büyütülebilir. Daha fazla bilgi için [tıklayınız](https://stonefly.com/resources/what-is-file-level-storage-vs-block-level-storage).  

##RAID (Redundant Array of Independent Disks):

Raid bir block sanallaştırma türüdür. Birden fazla diskle izin verilen input/output işlemlerinin iyileştirilmesini, dengelenmesini sağlamaktadır. Bu işlemler raid kontrol aracı (raid controller) ile olmaktadır. Raid kontrol aracı, işletim sistemi ve hard disklerin arasında bulunur. Mantıksal ünitelerin gruplandırılıp aktarılması sağlanmaktadır. Raidin seviyeleri bulunmaktadır. Her seviyenin kendine ait avantajı ve dezavantajı bulunmaktadır. Raid 0,1,3,5,7 hız ve güvenlik sağlamaktadır. Daha fazla bilgi için [tıklayınız](https://searchstorage.techtarget.com/definition/RAID).  


####RAID 0:

Sunucunun performansını iyileştirmek için kullanılır. Disk bölüştürme olarak bilinir. Kısaca bir yerden okuma/yazma yerine birden fazla yerden okuma/yazma yapabilmektedir. En az iki diske ihtiyaç duyar. Dezavantajı ise disklerden birisi bozulursa sıralama kayması nedeniyle veri kaybı yaşanır veya bozulmanın miktarı artar.

####RAID 1:

Veri kaybını engellemesi için avantajlı bir sistemdir. İki diskten birisine yazarken diğer diske de kopyalanmaktadır. Yani diskin sürekli bir kopyası oluşturulmaktadır. Dezavantajı ise sürekli bir yedekleme olmasından dolayı performansın düşük olmasına sebep olmaktadır.

####RAID 3:

Uzun süreli sıralı(sequential) veri akışlarında (medya yayıncılığı, video düzenlemeleri) kullanılır. Sıralı yazmasından dolayı küçük boyutlu veriler için performansızdır.


####RAID 5:

Genellikle şirketlerin nas ve sunucularında kullanılır. RAID 1'den daha performanslı çalışır. RAID 5'te veri üç veya daha fazla diske ayrılır. Eğer disk bozulmaya başlarsa veri diğer disklerden otomatik ve sorunsuz bir şekilde tekrar gelir. Sistem çalışırken disk değiştirme yapılabilmektedir. Yeni disk takıldığı zaman otomatik olarak veriyi yaymaktadır. Dezavantajı ise fazla kullanıcı olduğunda performans sorunu yaşanmaktadır.

####RAID 7:

RAID 3 ve RAID 4'ün özelliklerini bulundurmaktadır. Ek olarak saklama (cache) özelliği vardır.


##Donanım Sanallaştırma:

![Alt text](/katkida-bulunanlar/safa-bayar/sanallastırma/sanallastırma.md
)  

###Full Virtualization:

Donanımın bütünüyle simülasyonlaştırılmasıdır. Yazılıma, donanımın parçalarınından birinin farklı binary kodlaması kullanılmasına izin vermesidir.  

###Paravirtualization:

Bir donanımın simülasyon edilmesi yerine programın izole edilmiş bir ortamda çalıştırılmasıdır.  

###Hardware-assisted Virtualization:

Donanım da sanallaştırma için destek sağlayan CPU'ları ve performansını iyileştirmeye yardımcı olan diğer donanım bileşenlerini içerir. Bu sayede donanımı daha verimli kullanarak daha performanslı çalışmasını sağlar.  

![Alt text](/katkida-bulunanlar/safa-bayar/sanallastirma/images/Virtualization.png)  

###Wine:

Windows tarafından geliştirilmiş yazılım ve oyunların UNIX türevli sistemlerde çalıştırılması için bulunan özgür ve açık kaynak bir platformdur. Windows üzerinde çalışan programın çalışmasında kullanılan yazılım kütüphanelerini UNIX türevli bilgisayarlarda uyumlu olmasını sağlamaktadır. Bu kütüphaneler sayesinde donanım üzerinden sanallaştırma yapılmasını sağlar. Wine is not emulator tanımı ise wine'nın emülatör olmadığını söylemektedir. Wine birleşik katmanlı yapıda çalışabilen, POSIX standartlarına sahip işletim sistemlerinde çalışan  windows uygulamalarını çalıştırmaya yarayan bir araçtır. Windowsta ayrı bir sanal makina veya emulatör simüle yapılması yerine wine windowsda ki  uygulamayı POSIX'in içinden kullanmaktadır. Daha fazla bilgi için [tıklayınız](https://en.wikipedia.org/wiki/Wine_(software))  


###Long Mode:

Diğer bir deyişle bilgisayar mimarisinde bulunan x86-64 dizaynıdır. long mode, 64 bit işletim sistemlerinde 64 bitlik işlemler ve registerları kullanabilmesini sağlar. 32 veya 16 bitlik programlarda koruma moduna([Protected Mode](https://en.wikipedia.org/wiki/Protected_mode)) geçilip çalışması sağlanmaktadır.

###Docker:

Docker'dan bahsetmeden önce konteyner teknolojisinden bahsedilmesi gerekmektedir. Konteyner, 1960 ve 1970 seneleri arasında [chroot](https://en.wikipedia.org/wiki/Chroot) işlemlerinin gelişmesiyle ortaya çıkmıştır. chroot işlemlerinin şu anki ismi ise ([LXC](https://en.wikipedia.org/wiki/LXC))'dir. LXC'nin gelişmesinden sonra  docker ve sistem konteynerlaştırma çıkmıştır. Konteyner imajları çalışan işletim sistemleri üzerinde bir konteyner motoru(container engine) olmalıdır. Konteynerlaştırılmış uygulamalar bir çok konteyner imajı olarak oluşturulabilir ve konteynerlar birleşik yapıda çalışabilir.

Docker konteynerları ise sanallaştırılmış uygulama konteynerlarını işletim sistemleri üzerinden yönetmek için kullanılmaktadır. Bir docker konteynerı, uygulama servisini (fonksiyonlarının bütün kütüphanleri ile beraber), ayar dosyaları, bağımlılıkları ve diğer operasyon parçalarını bulundurmaktadır. Her konteyner işletim sisteminin servisi(process) aracılığıyla yönetilebilmektedir.

![Alt Text](/katkida-bulunanlar/safa-bayar/sanallastirma/images/ENSON2.png)

Docker'ın sanallaştırmadan en önemli farkı; sanallaştırma, sanallaştırma katmanın üzerinde bulunan bir işletim sisteminin yönetimini sağlamaktaydı. Docker ise sanallaştırma katmanı bulunmadan docker engine sayesinde üzerinde uygulama çalıştırmaya yarar.


###KVM (Kernel-based Virtual Machine):

Full-virtualization kullanarak linux kerneli sanallaştırma aracıyla(Hypervisor) sanal makina kurmaktadır.

Derste yapılan uygulamada genel olarak debian dağıtımı kullandık. Bu yüzden kodlar debian için geçerlidir.

``` sudo apt-get install qemu-kvm ```

qemu-kvm'i yüklemesini sağlamaktadır. Centos dağıtımında ismi değişmektedir.

``` qemu-img create test.img 500M ```

qemu-kvm aracılığıyla sanallaştırma yapılacak olan işletim sisteminin alanı oluşturulmaktadır. 'test.img' kısmında imaj dosyasının ismi verilmektedir. '500M' ise boyutu en fazla 500 megabayt olarak ayarlanmasını sağlar. İmaj dosyasının burada ki amacı okuma/yazma işlemlerini yapmasıdır. qemu-kvm'in kullandığı dosya formatı [qcow2](https://people.gnome.org/~markmc/qcow-image-format.html)'dir

``` qemu-system-x86_64 -m 1000 -boot d -cdrom test.img ```

Debian dağıtımlarının sürümlerinde farklılık gösterebilir. Benim kullanıdığım dağıtım sürümünde qemu-system-x86_64 programı kullanarak imaj dosyamızı sanallaştırarak ayrı bir işletim sistemi sunmamızı sağlamaktadır. '*-m 1000*' sistem tarafından ayrılması istenen bellek miktarını belirtmektedir. '*-boot d*' boot parametresi boot aşamasının nasıl olacağını ve sonrasında görüntünün nasıl verileceğini*(!!!! Görüntü çeşitlerinin linki)* belirlenmesini sağlar. Burada ki*(!!!!!!!)* 'd' ilk olarak cdromdan başlatılmasını söylemektedir. '*-cdrom test.img*' imaj olarak '*test.img*' kullanılmasını söylemektedir. Sonrasında okuma/yazma işlemleri test.img üzerine yapılmaktadır.  

Eğer bir arayüz üzerinden kontrol etmek isterseniz '*virt-manager*' adında paketi yükleyebilirsiniz. Buna ek olarak virsh adında bir kvm yönetim aracı da vardır. Virsh komut satırı üzerinden yönetimini kolaylaştırmaktır. Eğer yüklemek isterseniz [bağlantıyı](https://wiki.debian.org/KVM#Installation) takip edebilirsiniz.

Ders'te anlatılan konular bu kadardı. Eklemek istediğiniz bilgi veya düzeltme olursa [gitlab](https://gitlab.com/rection) hesabıma ekleyebilirsiniz.
