# Android Projelerinde API Anahtarını Güvenli Bir Şekilde Nasıl Saklarız?

## **İçindekiler**

- [local.properties Dosyasını Oluşturma](#localproperties-dosyasını-oluşturma)
- [API Anahtarını Gradle İle Yükleme](#api-anahtarını-gradle-i̇le-yükleme)
- [API Anahtarını Kodda Kullanma](#api-anahtarını-kodda-kullanma)
- [Peki Bu Yöntemle Başka Neleri Koruyabiliriz?](#peki-bu-yöntemle-başka-neleri-koruyabiliriz)

<br>

<table>
  <tr>
    <th style="text-align: left; font-weight: bold;">Yayınlanma Tarihi</th>
    <td style="text-align: left;">30 Ağustos 2024</td>
  </tr>
  <tr>
    <th style="text-align: left; font-weight: bold;">Son Güncelleme Tarihi</th>
    <td style="text-align: left;"></td>
  </tr>
  <tr>
    <th style="text-align: left; font-weight: bold;">Tahmini Okuma Süresi</th>
    <td style="text-align: left;">3 dakika</td>
  </tr>
</table>


![-----------------------------------------------------](../../../Readme%20Resources/Çizgi.png)

<div align="center">
  <img src="./Resources/1.webp" alt="API Anahtarlarımız Koruma"/>
</div>

<br>

API anahtarları, Android uygulamalarının dış hizmetlerle etkileşimde bulunmasını sağlayan önemli bilgilerdir. Ancak bu anahtarların
doğrudan kod içine gömülmesi güvenlik risklerine neden olabilir. Bu yazıda Android projelerinde API anahtarlarını güvenli bir şekilde
saklamanın en iyi uygulamalarını göstereceğim.


## local.properties Dosyasını Oluşturma

İlk adım olarak API anahtarlarınızı güvenli bir şekilde saklamak için `local.properties` dosyasını kullanacağız. Bu dosya projenizin
kök dizininde bulunur ve hassas verileri saklamak için ideal bir yerdir.

Öncelikle `local.properties` dosyasını projenizin kök dizininde oluşturun. Ardından API anahtarınızı bu dosyaya tanımlayın. **Örneğin:**

```
API_KEY=your_api_key_here
```

## API Anahtarını Gradle İle Yükleme

API anahtarını `local.properties` dosyasından yüklemek için Gradle dosyalarınızı düzenlemeniz gerekmektedir. Projede API anahtarınızı
kullanacağınız yere göre ilgili modül seviyesindeki `build.gradle` veya `build.gradle.kts` dosyasını açın ve aşağıdaki kodu ekleyin:

```gradle
android {
    ...
    buildTypes {

            // local.properties dosyasından API anahtarını yükleme
            Properties properties = new Properties()
            properties.load(project.rootProject.file("local.properties").newDataInputStream())

        debug{
            buildConfigField "String", "API_KEY", "\"${properties.getProperty("API_KEY")}\""
        }

        release {
            ...
            minifyEnabled true
            buildConfigField "String", "API_KEY", "\"${properties.getProperty("API_KEY")}\""
        }
    }
}
```

<br>

Bu kod parçası, API anahtarını `local.properties` dosyasından yükler ve `BuildConfig` sınıfına ekler. Bu sayede API anahtarınızı
güvenli bir şekilde kodunuzda kullanabilirsiniz.


## API Anahtarını Kodda Kullanma

API anahtarınızı projenizin kodunda kullanmak için `BuildConfig` sınıfını kullanabilirsiniz.
Örneğin `Retrofit` gibi bir kütüphane kullanarak API çağrıları yapacaksanız:

```kotlin
interface ApiService {
    @GET("data")
    suspend fun fetchData(@Query("api_key") apiKey: String): Response<Data>
}

// Burda BuildConfig sınıfı ile Api key'e ulaşıyoruz
val apiKey = BuildConfig.API_KEY

val apiService = Retrofit.Builder()
    .baseUrl(BASE_URL)
    .addConverterFactory(GsonConverterFactory.create())
    .build()
    .create(ApiService::class.java)
```

<br>

Bu şekilde API anahtarınızı güvenli bir şekilde saklayabilir ve kullanabilirsiniz. Bu yöntem API anahtarlarınızı projenizin
kaynak kodlarında doğrudan gömmek yerine daha güvenli bir şekilde saklamanıza olanak tanır.


## Peki Bu Yöntemle Başka Neleri Koruyabiliriz?

**Çeşitli Anahtarlar ve Gizli Bilgiler**

Projeler genellikle sadece API anahtarlarını değil, aynı zamanda diğer gizli bilgileri
de saklar. **Örneğin** bunlar Firebase veya diğer üçüncü taraf hizmetlere erişim anahtarları, veritabanı kimlik
bilgileri, kullanıcı adları, şifreler vb. olabilir.

Bu bilgileri de `local.properties` dosyasında saklayabilirsiniz. **Örneğin:**

```
FIREBASE_API_KEY=your_firebase_api_key
DATABASE_USERNAME=your_database_username
DATABASE_PASSWORD=your_database_password
```

<br>

Ardından `local.properties` dosyasından bu değerleri yükleyerek ve `buildConfigField` ile `BuildConfig`
sınıfına ekleyerek projenizde kullanabilirsiniz.

```gradle
android {
    ...
    buildTypes {
        release {
            ...
            buildConfigField "String", "FIREBASE_API_KEY", "\"${properties.getProperty("FIREBASE_API_KEY")}\""
            buildConfigField "String", "DATABASE_USERNAME", "\"${properties.getProperty("DATABASE_USERNAME")}\""
            buildConfigField "String", "DATABASE_PASSWORD", "\"${properties.getProperty("DATABASE_PASSWORD")}\""
        }
    }
}
```

<br>

**Sabit URL'ler**

Projeler genellikle sabit URL'leri veya endpoint'leri kullanır. Bu URL'leri `local.properties` dosyasında
tanımlayabilir ve kodunuzda bu değerleri kullanabilirsiniz. Özellikle test ve üretim ortamları arasında farklı URL'ler
kullanmanız gerekiyorsa bu yaklaşım çok kullanışlı olabilir.

**Diğer Hassas Bilgiler**

Projelerinizde kullanılan diğer hassas bilgileri de `local.properties` dosyasında saklayabilirsiniz.
**Örneğin** ödeme geçitleri veya diğer entegrasyonlar için gerekli olan özel anahtarlar veya tokenlar gibi.

**Keystore ve SSL Sertifikaları**

Projelerinizde kullanılan `SSL sertifikalarını` ve uygulama imzalarını saklamak için keystore
dosyalarınızı kullanabilirsiniz. Bu dosyaları güvenli bir şekilde saklayarak uygulamanızın güvenliğini artırabilirsiniz.

**Yapılandırma Dosyaları**

Projenizin diğer yapılandırma dosyalarını da güvenli bir şekilde saklayabilirsiniz. **Örneğin** uygulamanızın
farklı ortamlar için farklı yapılandırmalara sahip olması gerekiyorsa bu yapılandırmaları `local.properties` dosyasından yükleyebilirsiniz.

<br>

Bu yöntemlerle projenizin güvenliğini artırabilir ve hassas bilgilerinizi koruyabilirsiniz. Ancak dikkat edilmesi gereken
önemli bir nokta `local.properties` dosyasının proje deposuna eklenmemesi ve paylaşılmamasıdır. Bu dosya yalnızca yerel
geliştirme ortamınızda kullanılmalıdır.

<br>

Eğer bu makaleden keyif aldıysanız ve faydalı bulduysanız desteğiniz benim için çok değerli. Beni takip ederek veya bir kahve
ısmarlayarak takdirinizi gösterebilirsiniz. Desteğiniz, kaliteli içerik üretmeye devam etmem için beni motive ediyor.


![-----------------------------------------------------](../../../Readme%20Resources/Çizgi.png)

<a href="https://github.com/mustafatoktas/W.BE_RepoVisitorCounterAPI" target="_blank"> <img src="https://toktasoft.com/api/github2/repo-visitor-counter.php?repo=puhte7bnmsf4k5z&show_repo_name=1&show_date=1&show_brand=0" alt="Repo Visitor Counter"/> </a>

<a href="https://buymeacoffee.com/mustafatoktas" target="_blank"> <img src="./../../../Readme Resources/İletişim/Buy Me a Coffee.png" alt="Buy Me a Coffee" height="64"/> </a>


![-----------------------------------------------------](../../../Readme%20Resources/Çizgi.png)

## İletişim

<a href="mailto:info@mustafatoktas.com"              target="_blank"> <img src="./../../../Readme Resources/İletişim/Mail.png"     alt="Mail"     width="64"/> </a>
<a href="https://t.me/mustafatoktas00"               target="_blank"> <img src="./../../../Readme Resources/İletişim/Telegram.png" alt="Telegram" width="64"/> </a>
<a href="https://www.linkedin.com/in/mustafatoktas/" target="_blank"> <img src="./../../../Readme Resources/İletişim/LinkedIn.png" alt="LinkedIn" width="64"/> </a>