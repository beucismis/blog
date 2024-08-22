+++
title = "Dotfiles Rehberi"
date = 2024-08-22

[taxonomies]
categories = ["linux"]
+++

# Dotfiles Nedir?

Dotfiles (veya konfigürasyon dosyaları) temel olarak kişisel ve/veya sisteminize özgü parametreleri, ayarları ve tercih edilen değerleri tutan dosyalardır. Örneğin, favori editörünüzün varsayılan yazı fontunu ayarladınız. Bu font bilgisi programın önceden belirlediği özel bir konum ve dosyaya işlenir. GNU/Linux dağıtımlarında bu dosyalar, yönetici ve kullanıcı bazında olabilir. Genelde, eğer kullanıcı dizininde yapılandırma dosyası ayarlanmamışsa, araç doğrudan kurulum esnasında çıkardığı yapılandırma dosyasını varsayılan olarak alır. Ya da araçlar, kurulum yapılan kullanıcının ev dizinine bu dosyaları yerleştirir.

Bu tür dosya adları nokta karakteri ile başlayabilir. Mesela:

```
~/.bashrc
~/.gitconfig
~/.vimrc
```

Ya da `~/.config/` dizini altında bulunabilir. Mesela:

```
~/.config/sway/config
~/.config/waybar/config.jsonc
```

---

Bu dosyalar farklı formatlarda olabilir (JSON, TOML vb.). Dosyaların konumlarına ve yapılandırma bilgilerine `man` veya web sitelerinden ulaşabilirsiniz. Bu tür dosyaların amacı tamamen kolaylık sağlamaktır. Kod tabanına inmeden değişkenleri yapılandırabilmemizi sağlar.

Bu nedenle, dosyaları saklamak ve her yeniden sistem kurduğumuzda hızlıca eski ayarlarımıza dönmek isteyebiliriz. En pratik saklama yollarından biri, dosyaları uzak bir sunucuda tutmaktır. GitHub gibi servisler bu amaç için oldukça kullanışlıdır.

---

Pekala, dosyaları aldık, sakladık... Bunları pratik bir şekilde dağıtmanın yolu nedir?

# Dotfiles Yönetimi

Bu başlık altında `stow` ([https://gnu.org/software/stow](https://gnu.org/software/stow)) isimli araçtan bahsedeceğim. Bu araç, temelde `ln` gibi dosya ve dizinler için sembolik bağlantılar oluşturur. Stow'un kullanımı çok basit ve anlaşılır olduğu için yapılandırma dosyalarını dağıtmak ve yönetmek epey kolaydır. Kurmak ile başlayalım...

Arch Linux için:

```
pacman -S stow
```

Yapılandırma dosyalarınızı `stow` aracına bir yol gibi vermelisiniz. Örneğin Sway için dosya yapısı şu şekilde olmalıdır:

```
sway/.config/sway/config
```

Bu tür bir yapılandırmada aşağıdaki komutu çalıştırdığınızda `config` dosyasını ev dizinindeki `.config` dizini altına oluşturduğu `sway` dizini içine sembolik bağlantı olarak ekleyecektir:

```
stow sway
```

Oluşturacağınız yol yapısı her zaman dosyanın yapılandırma yolu gibi olmalıdır. İşte birkaç örnek:

```
bash/.bashrc
etc/pacman.d/mirrorlist
pip/.pip/pip.conf
```

Sembolik bağlantı eklemenin güzel yanı, ana yapılandırma dosyaları değiştirildiğinde sembolik bağlantıların da otomatik olarak güncellenmesidir. Bu sayede tek bir yerden tüm farklı dizinlerdeki yapılandırma dosyalarını değiştirebilirsiniz.

# Diğer

Tamamen `stow` ile dağıtılmaya uygun şekilde tasarladığım GitHub dotfiles depoma aşağıda vereceğim bağlantıdan ulaşabilirsiniz:

[https://github.com/beucismis/dotfiles](https://github.com/beucismis/dotfiles)

Ben biraz işin keyif pezevengi olduğum için kurulum betiği bile hazırladım. Betiğin [şu](https://github.com/beucismis/dotfiles/blob/main/install.sh#L27) satırında gördüğünüz gibi yapılandırma dosyalarımı dağıtıyorum.

---

Stow haricinde, özellikle dotfiles yönetimi için geliştirilmiş araçlar da mevcuttur. Ben çok ihtiyaç duymadım. Bazıları epey karmaşık olabilir, zaman harcamaya değmeyebilir. Yine de bakmak isterseniz, kaynakçaya ekleyeceğim.

# Kaynakça

- [https://gnu.org/software/stow/](https://gnu.org/software/stow)
- [https://dotfiles.github.io/utilities](https://dotfiles.github.io/utilities)
