## Docker Komutları

```
docker run
```

**run** komutu bir image'ı çalıştırmayı sağlar. Örneğin;

```
docker run node
```

dediğimizde node isimli **container** eğer bizim docker host'umuzda **varsa** çalıştırılır. Eğer yoksa **Docker Hub Repository** üzerinden bulunur indirilir ve çalıştırılır. Bir sonraki **docker run node** komutuyla beraber **docker host** üzerinden çalışmaya başlar.

```
docker ps
```

**ps** komutu çalışan tüm containerların listesini bilgileriyle beraber döker.

)

her bir container random **isim ve id** bilgisi alır. Eğer çalışan ya da çalışmayan tüm container'ların listesini görmek istersek. Bu durumda **-a** parametresini göndermemiz gerekir.

```
docker ps -a
```



aynı işlemi

```
docker container ls
```

```
docker container ls -a
```

ile de görebiliriz.

bu bize çalışan ve daha önce çalışmış ama durmuş tüm container'ların listesini verecektir.

```
docker stop containerName|containerID
```

çalışan bir container'ı durdurmak için kullanılır **containerName** ya da **containerID** bilgisini vermemiz yeterlidir.

Eğer çalışmayan docker container'larının boşuna yer kaplamasını istemiyorsak;

```
docker rm containerName|containerID
```

kullanabiliriz. Bu durumda sildiğimiz container'ları **docker ps -a** yaptığımızda listede görmeyeceğiz!

Aktif ve pasif olan tüm container'ları silmek için

```
docker container rm $(docker container ls -aq)
```

komutunu kullanabiliriz.

Peki indirdiğimiz ama kullanmadığımız **image** listesini nasıl görebiliriz? ya da **image listesini** nasıl görebiliriz?

```
docker images
```

bize docker hosts üzerinde bulunan tüm image listesini detayları ile getirir.



peki bu image listesinden bir image silmek istersek ne yapabiliriz?

```
docker rmi imageName|imageID
```

image'i silecektir.

**Önemli Not**
Bir image'i silmek için, bu image'i kullanan herhangi bir container olmaması gerekir. Bundan dolayı ilk olarak bu image'e bağlı tüm container'ları silip sonrasında bu komutu çalıştırmalısınız.



Daha önceden **docker run nginx** ile beraber image'i bilgisayarımıza indirmiş ve bu sırada **ubuntu** image'ini yüklemesini beklemiştik. Tabi ki indirme ve çalıştırma esnasında bir bekleme süresi geçti. Bunu yapmak istemiyorsak? Yani sadece image'i indirip bırakmak istiyorsak bu durumda.

```
docker pull imageName (nginx)
```

diyebiliriz. Bu durumda sadece indirip bırakır. Image'i çalıştırmaz!

### Attach and Detach

Normalde bir bir docker image run ettiğimizde bu container ile ilgili bilgileri ya da Logları ekrana basabilir. Burada container'ın gereksinimlerinden dolayı çalıştırdığımız terminal üzerinde bir çok loga rastlayabiliriz. Bu modun adına **attach mode** denilir.
İstersek bunu arkaplanda yapabiliriz. Yani bir uygulamayı arkaplanda çalışmasını sağlayabilir ve bu bizim terminal üzerinde herhangi bir log görmemize engel olur bu modun adına da **detach mode** denilir. Bunu yapmak için;

```
docker run -d imageName
```

bu bize ayağa kaldırdığı container'in ID bilgisini verecektir.



Eğer **detach** yapılmış bir container'ı **attach** moda geri sokmak istersek bunu için **attach** komutunu kullanabilirz.

```
docker attach containerID
```

**Not:**

docker komutu ile tanımladığınız herhangi bir komutta eğer **ID** bilgisine ihtiyaç duyuyorsanız bu ID bilgisinin ilk bir kaç karakterini tanımlamanız yeterli. Yukarıdaki örnek üzerinden gidecek olursak;

```
docker attach a043d
```

yazdığımızda bu bizim için **detach** moda sokulan container'ın ID bilgisine eşittir.

```
docker run -d --name webapp nginx:1.14-alpine
```

## Docker Run Komutu
### run -tag
Bir image pull ettiğimizde bu image belirli bir sürüme sahip olur. Bu sürümün adına **tag** denilir. Son sürüm indirildiğinde bu tag **latest** olacaktır.



### run -stdin (-it/interactive terminal)

Örneğin terminal üzerinde kullanıcıdan bir bilgi alan uygulamanız var ve bu uygulamayı dockerize ettiniz. Bu uygulamanın image adı **testApp** olsun;

```
docker run testApp
```

dediğinizde, çalışan uygulama sizden herhangi bir bilgi almadan sonlanacaktır. Çünkü Docker prompt default olarak kullanıcıdan bilgi almamaya programlıdır. Fakat bunu değiştirebiliriz.

```
docker run -i testApp
```

**-it** argümanını göndererek run ettiğiniz image'de bir user prompt varsa buna izin verir. **-i = interactive terminal** demektir.



### run -port mapping

Bir container ayaga kaldırdığımızda bu image bir porta sahip olur. Biz bu ayağa kaldırdığımız uygulamaya yani container'a dışarıdan erişmek istediğimizde bu portu kullanarak **erişemeyiz**. Bunun yerine **port mapping** yapmalıyız. Bunu yapmak oldukça basit.

```
docker run -p DIS_PORT:IC_PORT imageName
```

şeklinde düşünülebilir. yani

```
docker run -p 80:5000 webApp
```

şeklinde bir komut ile çalıştırdığımızda artık biz uygulamamıza (yani docker host/engine üzerindeki container'a) **80** portu üzerinden erişebiliriz. Fakat Docker Engine/Host içerisinde uygulamamız çalışırken **5000** üzerinden çalışmaya devam edecektir. Böylece portları maplemiş oluyoruz.

### run -volume mapping

Docker container içerisinde veriler herhangi bir şekilde kalıcı olamaz. Bundan dolayı dockerize ettiğiniz uygulamanız dosya sistemi üzerinde eğer veri saklamaya ihtiyaç duyuyorsa ya da container içerisinde veri tabanı barındıran bir sistem de olabilir (mysql, mongodb vs.). Bu durumda volume kullanarak persistency sağlıyoruz yani kalıcılık. Bunun için **container dışında** bir location seçerek **run** komutu ile beraber **volume mapping** yapıyoruz.

Bunu yapabilmek için;

```
docker run -v /opt/datadir:/var/lib/mysql mysql
```

dediğimizde **mySQL**'in kayıtları sakladığı dizin olan **/var/lib/mysql**'deki verileri docker host içerisinde **/opt/datadir** klasöründe tutacaktır. Bu **/opt/datadir** klasörü docker host/engine tarafından izin verilen klasörlerden olmalıdır. Verilen data klasörü Container **silinse bile bilgiler docker engine üzerinde kalmaya devam eder**.


### inspect

Docker ps komutu contariner'lar hakkinda birçok bilgiyi bizimle paylaşır. Fakat daha fazla bilgiye ihtiyaciniz olursa **inspect** komutu bu bilgileri bize sağlar.

```
docker inspect containerName
```

### Container logs
Özellikle -d **dettach** mod ile çalışan bir container'a ait logları görmek için logs komutunu kullanabilirsiniz.

```
docker logs containerName
```

### Image Tag

Bir image'e tag vermek istiyorsak bu oldukça kolay.

```
docker image tag imageID/imageName tagName
```
---
> *Kablosuzkedi - gokhandemi den alintidir.* 
