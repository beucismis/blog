+++
title = "crDroid ile Mobil Cihaz Güvenliği Rehberi"
date = 2022-04-08

[taxonomies]
categories = ["security"]
+++

# Girizgah

Bu rehberde başlıkta bahsi geçen işletim sistemi ve yardımcı araçların kurulumu Debian GNU/Linux dağıtımı üzerinden anlatılacaktır. Bu yüzden temel GNU/Linux bilgisine sahip olmanız tavsiye edilir. Bol şans...

**Sorumluluk Reddi**: Bu değiştirilmiş işletim sistemini ve diğer araçları kendi sorumluluğunuzda kullanın! Mobil cihazınıza gelebilecek olası zararlardan yazar sorumlu değildir.

---

[TWRP](https://twrp.me) (Team Win Recovery Project), yardımcı araçlar sunan gelişmiş bir kurtarma menüsüdür. Mobil cihazlar için tasarlanmış özel işletim sistemlerini yükleme, yedek alma, alınan yedeği geri yükleme vb. işlevlere sahiptir.

[crDroid](https://crdroid.net), mobil cihazlarda kullanabileceğiniz özgür bir Android işletim sistemidir. LineageOS tabanlıdır. Gönüllü bakıcılar tarafından geliştirilir ve yayımlanır. Google'ın dağıttığı ve üreticilere zorunlu kıldığı bağımlılıklar ve fazlalıklar olmadan cihazınıza kurabilirsiniz. crDroid, verileriniz ve cihazınız üzerinde hakimiyet sahibi olmanızı sağlar.

[Magisk](https://github.com/topjohnwu/Magisk), cihazlara kök kullanıcı yetkisi vermek için kullanılan özgür bir araç. Köklendirme, temel olarak telefonun gerçek sağlayıcısı tarafından engellenen özelliklere erişmek için kendinize ayrıcalıklar vermektir. Eğer bilinçli bir kullanıcı değilseniz bu işlem sizin için tehlikeli açıklar yaratabilir.

[microG](https://microg.org) ise Google Play hizmetlerinin yerini alan, bir nevi taklit eden özgür bir projedir. microG, mobil cihaz kullanıcılarının, Google Play hizmetlerine kıyasla cihaz etkinliklerini daha az takip ederek Google mobil hizmetlerine erişmesine olanak tanır.

---

**Not 1**: Magisk ve microG araçlarını kurmak opsiyoneldir.

**Not 2**: Bu rehberde anlatılan kurulumlar [Xiaomi Redmi Note 8](https://en.wikipedia.org/wiki/Redmi_Note_8) donanımı (yazının devamında Ginkgo olarak bahsedilecektir) temel alınarak 8 Nisan-10 Nisan 2022 tarihleri arasında hazırlanmıştır. Kurulum adımları temelde çoğu farklı mobil cihaz ve işletim sistemi için benzerlik gösterir. Makalede verilen bağlantıların güncelliğini kontrol ediniz! Daha fazlası için SSS bölümünü ziyaret edin.

**Not: 3**: Başlamadan önce telefonunuzda bulunan tüm kişisel verilerinizi yedekleyin!

# Ön Hazırlık

## Geliştirici Seçenekleri

Cihazın, `Ayarlar > Telefon Hakkında` kısmına girip `MIUI sürüm numarası`na 8 kere bastıktan sonra Ayarlar menüsü altına `Geliştirici Seçenekleri` eklenecektir. Bu yöntem cihazdan cihaza ve sürümden sürüme farklılık gösterebilir.

## USB Hata Ayıklama

Mobil cihaz ile bilgisayar arasında iletişim kurabilmek için `USB hata ayıklama` seçeneğinin aktif olması gerekmektedir. `Geliştirici Seçenekleri` menüsüne girip `MI Kilit açma durumu` seçeneğine basın. Geçici bir hesap ekleyin. `USB hata ayıklama` seçeneğini aktifleştirin. Bu aşamadan sonra tekrar hesap eklememeniz gerekmektedir.

## Bootloder Kilidi

Bu kilit cihazların üzerinde üçüncü şahısların herhangi bir işlem yapmaması adına üretici firmalar tarafından kullanılır. Xiaomi cihazlarda kilit açmak için maalesef özgür olmayan ve Windows bağımlısı MI Unlock Tool aracını kullanıyoruz. İlerleyen zamanlarda daha iyi yöntemler keşfedersem eklerim.

**Dikkat**: Kilit açıldıktan sonra mobil cihaz fabrika ayarlarına dönmektedir. Bu işlemden önce özel verilerinizi yedeklediğinizden emin olun. Devam edelim...

```
- Kurtarma modu: Güç + Ses Açma
- Önyükleyici modu: Güç + Ses Kısma
```

Programı çalıştırın. Feragatnameyi kabul edin. MI hesabınıza giriş yapın. Mobil cihazınızı kapatın daha sonra USB girişinden bilgisayara bağlayın. Ses kısma ve güç tuşuna aynı anda basarak cihazı Fastboot moduna geçirin. Artık tıklanabilir olan `Unlock` düğmesine tıklayın. Karşınıza gelen güvenlik uyarısını okuyun ve devam etmek istiyorsanız yine kilidi açmak üzere `Unlock anyway` düğmesine tıklayın. Bu işlemin tamamlanması için yaklaşık 168~ saat beklemeniz gerekli. Eğer bekleme süresi dolduysa cihaz kilidi açılacaktır.

## ADB ve Fastboot Kurulumu

TWRP kurulumu için ADB ve Fastboot araçlarına ihtiycımız var. Bu araçları bilgisayarınıza kurun. Debian ya da Debian tabanlı bir dağıtım kullanıyorsanız aşağıdaki komut dizisini kullanabilirsiniz.

```
apt install android-tools-adb android-tools-fastboot
```

Arch Linux için:

```
pacman -S android-tools
```

# Kurulum

## TWRP Kurulumu

crDroid ve Magisk kurulumu için TWRP'ye ihtiyacımız var. Öncelikle mobil cihazınıza uygun TWRP imajını burdan en güncel sürümü seçin ve indirin. Ginkgo için [bu](https://dl.twrp.me/ginkgo) sayfa. Mobil cihazı USB girişinden bilgisayara bağladayın. Aşağıdaki komut ile cihazları listeleyin.

```
adb devices -l
```

Örnek çıktı:

```
List of devices attached
1426290e device usb:1-3 product:ginkgo model:Redmi_Note_8 device:ginkgo transport_id:11
```

Eğer mobil cihazınız listede yoksa Bootloder Kilidi Açma adımına tekrar dönün. Sorun yoksa aşağıdaki komut ile cihazı önyükleyici modunda başlatın. Aynı işlemi telefon kapalı durumdayken güç ve ses kısma tuşlarına aynı anda basarak gerçekleştirebilirsiniz.

```
adb reboot bootloader
```

İndirdiğiniz TWRP imajının olduğu dizine gidin ve imaj dosyasını gönderin.

```
fastboot flash recovery twrp-3.6.1_9-0-ginkgo.img
```

İşlem tamamlandıktan sonra cihazı yeniden başlatın.

```
fastboot reboot
```

TWRP imajının sağlıklı bir şekilde çalıştığından emin olmak için aşağıdaki komut ile mobil cihazı kurtarma moduna alın. Eğer her şey yolunda ise TWRP menüsü sizi selamlayacaktır. Aynı işlemi cihaz kapalı durumdayken güç ve ses kapama tuşlarına aynı anda basarak gerçekleştirebilirsiniz.

```
adb reboot recovery
```

Mobil cihazı yeniden açmak için, `Reboot > System` yolunu izleyin.

## crDroid Kurulumu

Bu bölümde harici bir disk aygıtına ihtiyacımız var. İster SD kart isterseniz mobil cihazlara takilabilir USB depolama aygıtlarını tercih edebilirsiniz. Mobil cihazınıza uygun crDroid sürümünü [buradan](https://crdroid.net/downloads) bulun ve indirin. crDroid 8 yani Android 12 tabanlı en güncel sürümü seçebileceğiniz gibi daha eski sürümleri de tercih edebilirsiniz. Ginkgo için [bu](https://crdroid.net/ginkgo/8) sayfa. Ben en güncel sürümü tercih edeceğim. Eğer şanslıysanız bakıcınız her ay düzenli güncelleme yayımlayacaktır. `Download latest version` düğmesine basarak en güncel sürümü indirin. Ek olarak indirdiğimiz dosyanın bütünlüğünü doğrulamak için aynı sayfada bulunan SHA256 kodunu işletim sistemini indirdiğiniz dizinde bir dosyaya kaydedin.

İndirdiğiniz işletim sistemi dosyasının SHA256 kodunu oluşturun. Daha sonra dosyaya kaydettiğiniz kod ile karşılaştırın. Kodlar aynı ise işleme devam edebiliriz.

```
sha256sum crDroidAndroid-12.1-20220409-ginkgo-v8.4.zip
```

crDroid işletim sistemini harici depolama aygıtına gönderin. Depolama aygıtını mobil cihazınıza yerleştirin ve cihazı kurtarma modunda başlatın.

```
adb reboot recovery
```

TWRP menüsünde `Wipe` seçeneğine girin. `Advanced Wipe` düğmesine basın. `Dalvik / ART Cache`, System ve Data seçeneklerini işaretleyip, `Swipe to Wipe` düğmesini kaydırarak cihazı temizleyin. Geri gelin. `Install` seçeneğine girin. `Select Storage` düğmesine basın, harici depolama aygıtını seçin. Dosyalar içinden crDroid işletim sistemini seçin. `Swipe to confirm Flash` düğmesini kaydırarak kurulum işlemine başlayın. İşlem tamamlandıktan sonra cihazı yeniden başlatmayın, menüye geri gelin ve Magisk kurulumu ile devam edelim.

## Magisk Kurulumu (Opsiyonel)

Magisk uygulamasının GitHub [releases](https://github.com/topjohnwu/Magisk/releases) adresinden son sürümün `.apk` uzantılı dosyasını indirin. Dosyayı `.zip` olarak yeniden adlandırın. Dosyayı depolama aygıtına gönderin. Depolama aygıtını çıkarmadan göndermek isterseniz aşağıdaki komutu kullanabilirsiniz. Göndereceğiniz dosya yolu depolama aygıtına göre değişebilir.

```
adb push Magisk-v24.3.zip /sdcard1
```

Menüden tekrar `Install` seçeneğine girin. Depolama aygıtını seçin. Magisk dosyasını seçin. `Swipe to confirm Flash` düğmesini kaydırarak uygulamayı kurun. `Reboot System` düğmesine basarak sistemi yeniden başlatın. crDroid kurulumu başarılı olduysa karşınıza crDroid'e özel yüklenme ekranı gelecektir. `Welcome to crDroid` yazısına geldikten sonra devam edin ve kurulumu tamamlayın.

Son olarak Magisk dosyasının uzantısını `.apk` olarak yeniden adlandırın ve yüklemeyi tamamlayın.

## microG Kurulumu (Opsiyonel)

[MinMicroG](https://github.com/FriendlyNeighborhoodShane/MinMicroG), microG uygulamalarını yüklemeyi kolaylaştırmak için çeşitli paketler sunar. README.md dosyasını okuyarak size uygun olan paketi seçebilirsiniz. Ben Minimal paketi yükleyeceğim. MinMicroG [releases](https://github.com/FriendlyNeighborhoodShane/MinMicroG_releases/releases) deposunda gidiyoruz ve seçtiğimiz paketi mobil cihazımıza indiriyoruz. İndirdiğimiz `.zip` uzantılı dosyanın başına `system-` eki ekleyerek yeniden adlandırın.

```
Önce: MinMicroG-Minimal-2.11.1-20220202153430.zip
Sonra: system-MinMicroG-Minimal-2.11.1-20220202153430.zip
```

Magisk uygulamasını açın. Ayarlar menüsüne gidin. `HTTPS üzerinde DNS` ve `Sistemsiz hosts` seçeneklerine basarak aktifleştirin. Bunlar ilerde işinize yarayacaktır. Geri gelin. Alt kısımdan modüller seçeneğine girin. `Dahili depolamadan yükle` düğmesine basın. Yeniden adlandırdığınız MinMicroG paketini seçin, kurulmasını bekleyin. Sistemi yeniden başlatın.

Not: Paketi kaldırmak için başına `system-` yerine `uninstall-` ekleyin.

Uygulama menüsünden `microG Settings` uygulamasını açın. `Self-Check` seçeneğine basın. Sağda bulunan bütün yeşik tikler aktif oluncaya dek izinleri verin. Daha sonra geri gelin. Çoğu uygulama bildirim göndermek için Google servislerini kullanır. Bu özelliği aktifleştirmek içi `Cloud Messaging` seçeneğini açın.

Bu aşamadan sonra yedek almananızı şiddetle tavsiye ederim. Yedek almak için mobil cihazı kurtarma modunda başlatın. TWRP menüsünden `Backup` düğmesine basın. `Boot`, `System Image` ve `Data (excl. storage)` seçeneklerinin işaretlendiğinden emin olun. Dilerseniz Options menüsünden `Enable compression` seçeneğini aktif edebilirsiniz. `Swipe to Backup` düğmesini kaydırarak yedek alın.

Menüden `Restore` seçeneğini kullanarak aldığın yedeğe geri dönebilirsin.

# Diğer

## Son Sözler

Tebrik ederim, tüm adımları başarıyla gerçekleştirdik. Başka rehberlerde görüşmek üzere...

## crDroid Nasıl Güncellenir

Yakında...

## Sıkça Sorulan Sorular

- S: Telefonuma uygun crDorid sürümü yok ne yapmalıyım?
+ C: crDroid altarnatifi diğer Android işletim sistemlerine göz atabilirsin.
- S: ADB ve Fastboot araçlarını Windows yüklü makinede kullanabilir mi?
+ C: Evet kullanılabilir. Araçların Windows sürümlerine [buradan](https://developer.android.com/studio/releases/platform-tools) ulaşabilirsin.

## Kaynaklar

Aşağıdaki kaynaklar bu rehberi yazmamada rehberlik etmiştir. Emeği geçen herkese teşekkür ediyorum.

- [https://guvenlik.oyd.org.tr/cihaz_guvenligi/lineageos.html](https://guvenlik.oyd.org.tr/cihaz_guvenligi/lineageos.html)
