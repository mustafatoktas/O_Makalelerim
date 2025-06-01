# JVM, JRE ve JDK Nedir?

## **İçindekiler**

- [JVM, JRE ve JDK Nedir?](#jvm-jre-ve-jdk-nedir)

<br>

<table>
  <tr>
    <th style="font-weight: bold;">Yayınlanma Tarihi</th>
    <td>27 Ağustos 2024</td>
  </tr>
  <tr>
    <th style="font-weight: bold;">Son Güncelleme Tarihi</th>
    <td></td>
  </tr>
  <tr>
    <th style="font-weight: bold;">Tahmini Okuma Süresi</th>
    <td>2 dakika</td>
  </tr>
</table>


![-----------------------------------------------------](../../../Readme%20Resources/Line.png)

Bu yazıda Java ile geliştirme yapan herkesin bilmesi gereken en basit kavramlardan olan JVM, JRE ve JDK kavramları üzerinde duracağız.

<br>

`JVM` **(Java Virtual Machine)** için java programının çalıştığı platform ile java programı arasında soyut bir ara katman diyebiliriz.
JVM, platforma bağımlı olarak çalışır. Yani geliştirme yapacağınız platforma (Windows,Linux,Mac) göre farklı implementasyonları mevcuttur.
JVM, yazdığımız .java uzantılı dosyaları anlamaz onun yerine derlenmiş .class uzantılı dosyaları anlar. Çünkü .class uzantılı dosyalar
içlerinde `bytecode`'lar içerirler. Bu özelik sayesinde Java'da `Write once, run everywhere` özeliğini kullanabiliyoruz. Yani bu şu demek oluyor,
bizim windows bir makinede yazmış olduğumuz uygulama önce compiler tarafından bytecode'lara çevriliyor daha sonra bu bytecode'lar diğer platformlarda
kurulu olan JVM'ler aracılığıyla tüm platformlarda çalışıyor.

<img src="./Resources/1.webp" alt="JVM Çalışma Prensibi" height="300"/>

<br>

`JRE` **(Java Runtime Enviroment)** ise java programlama dili ile yazılmış olan uygulama ve `applet`'lerin çalışmasını sağlayan bileşenler ile JVM'e kütüphaneler sağlar.
Derlenmiş bytecode'lar direk olarak CPU üzerinde çalışmazlar. CPU tarafından anlaşılması için aradaki JVM bytecode'ları okunabilir makine kodları olarak yorumlar.
Aslında java bytecode'ların bütün platformalarda çalışması JRE sayesindedir. İçerisinde JVM, Core kitaplıkları ve Java yazılımında yazılan uygulamaları
ve küçük uygulamaları çalıştırmak için diğer ek bileşenleri içerir. JRE'nin görevi Java kodları derlendikten sonra bir ara dil olarak kabul edilen
Java baytcode'larını oluşturmaktır. Bu baytcode'lar bütün işletim sistemleri için aynıdır.

<img src="./Resources/2.webp" alt="JDK - JRE - JVM Genel Resim" height="300"/>

<br>

`JDK` **(Java Development Kit)** ise java da geliştirme yapmak isteyen her developer'ın mutlaka indirmesi gereken bir bileşendir.
Kısaca java için `SDK` (Software Development Kit) diyebiliriz. Hem yorumlayıcı hem de derleyici görevini üstlenmektedir
JRE ile birlikte appletleri ve uygulamaları geliştirirken zorunlu olan `debuggers` ve `compilers` gibi geliştirme araçlarını da bünyesinde bulundurur.

O zaman Özetle şu şekilde düşünebiliriz:

`JRE` = `JVM` + `Java Kütüphaneleri`

`JDK` = `JRE` + `compiler` + `debugger`


![-----------------------------------------------------](../../../Readme%20Resources/Line.png)

## İletişim

<a href="mailto:info@mustafatoktas.com"             ><img src="../../../Readme Resources/Communication/Mail.png"     alt="Mail"     width="64"/></a>
<a href="https://t.me/mustafatoktas00"              ><img src="../../../Readme Resources/Communication/Telegram.png" alt="Telegram" width="64"/></a>
<a href="https://www.linkedin.com/in/mustafatoktas/"><img src="../../../Readme Resources/Communication/LinkedIn.png" alt="LinkedIn" width="64"/></a>

<div align="center">
  <a href="#jvm-jre-ve-jdk-nedir"><img src="../../../Readme Resources/Back to Top.png" alt="Back to Top" height="64"/></a>
</div>