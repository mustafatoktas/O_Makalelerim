# Android Uygulamalara Root ve Emülatör Tespiti Kontrolü Ekleme

## İçindekiler

- [Root Tespiti](#root-tespiti)
- [Emülatör Tespiti](#emülatör-tespiti)

<br>

<table>
  <tr>
    <th>Yayınlanma Tarihi</th>
    <td>2 Eylül 2024</td>
  </tr>
  <tr>
    <th>Son Güncelleme Tarihi</th>
    <td></td>
  </tr>
  <tr>
    <th>Tahmini Okuma Süresi</th>
    <td>4 dakika</td>
  </tr>
</table>


![—————————————————————————————————————————————————](../../../Readme%20Resources/Line.png)

Android uygulama geliştirme dünyasında güvenlik oldukça önemlidir. Uygulamanızın güvenli bir ortamda çalışmasını sağlamak ve kötü niyetli
kullanıcıların uygulamanızı rootlanmış cihazlarda ve emülatörlerde çalıştırmasını önlemek esastır. Bu makale Android uygulamanıza root ve emülatör
tespiti kontrolleri eklemenin yollarını göstereceğim. Böylece uygulamanızın güvenliğini ve güvenilirliğini artırabileceksiniz.


## Root Tespiti

### 1. Dosya Yolu Analizi ile Tespit

Android cihazını rootlamak, kullanıcılara sistem dosyalarına ve ayarlarına erişim ve bunları değiştirme yetkisi veren `süper kullanıcı`
veya `yönetici ayrıcalıkları` sağlar. Rootlama, doğası gereği kötü niyetli olmasa da güvenlik riskleri oluşturabilir.
Android uygulamanıza root tespiti eklemek için aşağıdaki gibi bir kod kullanabilirsiniz.

```kotlin
private fun isDeviceRooted(): Boolean {
    val superuserApk = File("/system/app/Supperuser.apk")
    val suBinary = File("/system/bin/su")

    return superuserApk.exists() || suBinary.exists()
}
```

<br>

Bu kodda `Superuser.apk` ve `su` dosyalarının varlığı kontrol ediliyor. Bu dosyalardan herhangi birinin varlığı cihazın rootlanmış olduğunu gösterir.


### 2. Sistem Komutu Yürütme ile Tespit

Verilen kod parçası bir Android cihazda root erişimini tespit etmek için bir sistem komutunu `süper kullanıcı`
ayrıcalıklarıyla çalıştırmayı denemek amacıyla tasarlanmıştır.

```kotlin
var isRooted = false
var process : Process? = null
try {
    process = Runtime.getRuntime().exec(command="su")

    //Root Detected
    isRooted = true

} catch (e: Exception) {

    // Not Root Detected
    e.printStackTrace()
    
} finally {
    if (process != null) {
        try {
            process.destroy()
        } catch (e: Exception) {
            e.printStackTrace()
        }
    }
}
```

<br>

Bu kod, Runtime kullanarak `su` komutunu yürütmeyi dener. Bu komutun başarılı bir şekilde çalıştırılması cihazın `root` **(süper kullanıcı)** erişimine
sahip olduğunu gösterir. Komutun yürütülmesi sırasında bir `exception` **(istisna)** fırlatılırsa genellikle root erişiminin
olmadığı ya da başka bir sorun olduğu anlamına gelir. Bu durumda kod, `catch` bloğu kullanarak istisnayı yakalar.
Hata ayıklamaya yardımcı olmak için `stack trace` **(yığın izini)** yazdırır. `su` komutunun başarılı olup olmadığına
bakılmaksızın `finally` bloğu mevcut process'in düzgün bir şekilde ele alınmasını sağlar. `process` null değilse sistem kaynaklarını
serbest bırakmak için onu sonlandırır.


### 3. BusyBox Uygulaması Kontrolü ile Tespit

Bu kod bir Android cihazda root erişimini tespit etmek için `BusyBox` uygulamasının varlığını kontrol eder. Cihazın paket yöneticisine
erişerek busy box uygulamasının paket bilgilerini alır. Paket bilgileri başarıyla alındıysa bu BusyBox uygulamasının cihazda yüklü olduğunu
ve genellikle root erişimi ile ilişkilendirildiğini gösterir. Bu durumda `isRooted` bayrağı `true` olarak ayarlanır ve root erişiminin
tespit edildiği anlamına gelir.

```kotlin
var isRooted = false
var packageName = "stericson.busybox"
val pm = requireActivity().packageManager
try {
    pm.getPackageInfo(packageName, PackageManager.GET_ACTIVITIES)

    //Root Detected
    isRooted = true
}
catch(e: PackageManager.NameNotFoundException) {
    //App not installed
    e.printStackTrace()
}
```

<br>

Bu kod Android cihazlarında basit ama etkili bir root tespit mekanizması sağlamak amacıyla BusyBox uygulamasının varlığını
root erişimi göstergesi olarak kullanır.


## Emülatör Tespiti

Emülatörler, Android uygulama testi ve geliştirmesi için kullanılan sanal cihazlardır. Emülatörleri tespit etmek uygulamanızın
amaçlanmadığı bir ortamda çalışmasını engellemeye yardımcı olabilir. Uygulamanızın bir emülatörde çalışıp çalışmadığını kontrol
etmek için aşağıdaki kod parçasını kullanabilirsiniz.

```kotlin
private fun checkIsEmulator() {
    if (BuildConfig.BUILD_TYPE.equals("debug", true)) {
        return
    }

    val result = (Build.FINGERPRINT.startsWith("google/sdk_gphone_")
            && Build.FINGERPRINT.endsWith(":user/release-keys")
            && Build.MANUFACTURER == "Google" && Build.PRODUCT.startsWith("sdk_gphone") && Build.BRAND == "google"
            && Build.MODEL.startsWith("sdk_gphone"))
            || Build.FINGERPRINT.startsWith("generic")
            || Build.FINGERPRINT.startsWith("unknown")
            || Build.HARDWARE.contains("goldfish")
            || Build.HARDWARE.contains("ranchu")
            || Build.MODEL.contains("google_sdk")
            || Build.MODEL.contains("Emulator")
            || Build.MODEL.contains("Android SDK built for x86")
            || Build.MANUFACTURER.contains("Genymotion")
            || Build.HOST == "Build2" //MSI App Player
            || Build.BRAND.startsWith("generic") && Build.DEVICE.startsWith("generic")
            || Build.PRODUCT.contains("sdk_google")
            || Build.PRODUCT == "google_sdk"
            || Build.PRODUCT.contains("sdk")
            || Build.PRODUCT.contains("sdk_x86")
            || Build.PRODUCT.contains("vbox86p")
            || Build.PRODUCT.contains("emulator")
            || Build.PRODUCT.contains("simulator")

    if (result) {
        requireActivity().finish()
    }
}
```

<br>

Root ve emülatör tespiti kontrollerini eklemek uygulamanızın güvenliğini ve güvenilirliğini artırmak adına değerli bir adımdır.
Bu kontrolleri uygulayarak uygulamanızın rootlanmış cihazlarda veya emülatörlerde çalışmasını engelleyebilir, potansiyel güvenlik
tehditlerinden koruyabilir ve kullanıcılarınız için daha kontrollü bir ortam sağlayabilirsiniz.

<br>

Bu kontrollerin faydalı olmasına rağmen tamamen hatasız olmadığını unutmayın. Bu nedenle güvenliği artırmak için bu kontrolleri
şifreleme, `obfuscation` **(kod karıştırma)** ve sunucu tarafı kontrolleri gibi diğer güvenlik önlemleriyle birleştirmek önemlidir.
Son olarak potansiyel güvenlik risklerinin önüne geçmek ve güvenli ve güvenilir bir kullanıcı deneyimi sunmak için en iyi
uygulama düzenli güncellemeler yapmaktır.


![—————————————————————————————————————————————————](../../../Readme%20Resources/Line.png)

## İletişim

<a href="mailto:info@mustafatoktas.com"             ><img src="../../../Readme Resources/Contact/Mail.png"     alt="Mail"     width="64"/></a>
<a href="https://t.me/mustafatoktas00"              ><img src="../../../Readme Resources/Contact/Telegram.png" alt="Telegram" width="64"/></a>
<a href="https://www.linkedin.com/in/mustafatoktas/"><img src="../../../Readme Resources/Contact/LinkedIn.png" alt="LinkedIn" width="64"/></a>

<div align="center">
  <a href="#android-uygulamalara-root-ve-emülatör-tespiti-kontrolü-ekleme"><img src="../../../Readme Resources/Back to Top.png" alt="Back to Top" height="64"/></a>
</div>
