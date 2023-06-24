
# Portainer ve Uptime Kuma ile Docker Monitoring

Docker kullanarak Portainer ve Uptime Kuma birleştirip sistemimizi izleyip aynı zamanda Portainer sağladığı imkanlar ile sistemimizde ki konteyner(container) yönetme, Uptime Kuma ile izleme imkanımız olacak.
Sanal Makinemin ip adresi :“192.168.1.122”

Docker üstüne **Portainer** ve **Uptime Kuma** kullanarak sistem izleme “monitoring” kolayca nasıl yapabileceğimizi anlatmaya çalışacağım.

## Kurulum

```bash
git clone https://github.com/magwyen/portainer-kuma-monitoring.git
cd portainer-kuma-monitoring/
docker-compose up -d
```

## Adresler :

          Portainer  : https://192.168.1.122:9443

          Uptime Kuma : http://192.168.1.122:3001

isterseniz .yml dosyasından bu portları değiştirebilirsiniz.

Bu iki adrese girdiğinizde ilk önce sizden bir kullanıcı oluşturmanızı isteyecektir bunları oluşturduktan sonra. Sisteme giriş yapmış olacaksınız.

Sağ üstteki menüden “Settings” bölümüne girdiğinizde. Bu menüden Docker Host kısmına bölümüne geldiğinizde.

![Untitled](https://github.com/magwyen/portainer-kuma-monitoring/blob/main/img/kuma4.png)

Setup Docker Host Bölüm tıklayın ve karşınıza “Setup Doker Host” penceresi açılacak. Burada seçtiğiniz bir isim verin ve diğer ayarlara dokunmadan “Test” buttonuna tıklayın. Eğer hiç bir sorun yoksa sağ altta “Connected Successfully.”  mesajı çıkacak eğer bu mesaj gördüyseniz Korkmayın oldu.

Bu ayar sistemde ki docker konteyner izlemek için eğer mevcut sisteminizde konteyner teknolojisi kullanmıyorsanız bu adımı atlayabilirsiniz veya sonraya bırakabilirsiniz.

![Untitled](https://github.com/magwyen/portainer-kuma-monitoring/blob/main/img/kuma1.png)

**Uptime Kuma** bir çok farklı metot sunuyor bu “Monitor Type” sizin için uygun olanı seçip ip veya domain isimleri ile izleme yapabilirsiniz.

## Test

Şimdi sistemime PostgreSQL ve pgAdmin4 docker-compose.yml dosyasını çalıştırıp izlemeye çalışacağız.

```bash
git clone https://github.com/magwyen/postgres-pgadmin-docker.git
cd postgres-pgadmin-docker
docker-compose up -d
```

Kurulumu kontrol etmek için;

```bash
$ docker-compose ps
   Name                 Command              State                             Ports
--------------------------------------------------------------------------------------------------------------
PostgreSQL   docker-entrypoint.sh postgres   Up      0.0.0.0:5432->5432/tcp,:::5432->5432/tcp
pgAdmin4     /entrypoint.sh                  Up      443/tcp, 0.0.0.0:5050->5050/tcp,:::5050->5050/tcp, 80/tcp
```

State bölümünde Up olduğunu görüyoruz fakat Uptime Kuma bu iki servisi izlememiz için bu container ID bilgileri lazım onu öğrenmek için “docker inspect” kullanacağız.

```bash
$ docker inspect --format="{{.Id}}" PostgreSQL
b8fbaef7e4f4f109a4d802eb117599bca1609619c6ff5e278cb6df3b67ad37da
$ docker inspect --format="{{.Id}}" pgAdmin4
9560489ddc007e27264734b09f8f337c95faad68209ea23223b53ef8902d0b2b

```

Burada ilk önce docker-compose ps veya docker ps kullanarak containerların isimlerini öğrendik. Sonrasında inspect kullanarak container ID’lerini öğrenmiş olduk.

Şimdi bunları **Uptime Kum**aya eklemek kaldı. 192.168.1.122:3001 adresine girdiğimizde sol üst bölümde “ Add New Monitör “ yazısını göreceksiniz buraya tıklayarak. Açılan menüden;

**Monitor Type** : Docker Container

**Friendly Name** : PostgreSQL

**Container Name / ID** : Buraya docker ispect ile öğrendiğimiz ID numarasını yazıcaz.

**Docker Host** : Burayı önceden oluşturduk bu oluşturduğumuz Docker Host seçiyoruz.

**Heartbeat Interval (Check every 30 seconds)** : Bu bölümlerde ki süreler tamamen size kalmış ben “30” yazacağım ve save buttonuna basıyoruz.

![Untitled](https://github.com/magwyen/portainer-kuma-monitoring/blob/main/img/kuma2.png)

Aynı işlemi pgAdmin4 içinde yapıp onu da ekliyorum.

![Untitled](https://github.com/magwyen/portainer-kuma-monitoring/blob/main/img/kuma3.png)

**Uptime kuma**nın ekstra bir çok özelliği daha var en önemlisi ise size telegram, whatsapp ve discord gibi uygulamalar üstünden sisteminizde bir terslik olduğunda size bildirim gönderebiliyor. Tabi bununu için ayarlamalar yapmanız gerekiyor. 

Bu sistemi **Portainer** ile birleştirip sistemi izlerken aynı zamanda sahip olduğunuz docker container da sıkıntı çıktığında buradan da müdahale edebilirsiniz. **Portainer** web ara yüzünde istediğiniz bir konteyner’a girip yeniden başlatma, durdurma veya düzenleme imkanınızda oluyor.

![Untitled](https://github.com/magwyen/portainer-kuma-monitoring/blob/main/img/portainer.png)
