
# **Mikroservisler ve En Çok Kullanılan Design Pattern’ler (Tasarım Desenleri)**

Bu makale, mikroservis mimarisi ile yazılım geliştirirken kullanılan en iyi design pattern’leri (tasarım desenleri) kapsamlı bir şekilde incelemektedir. Gerçek dünya senaryolarıyla desteklenen içerik, yazılım geliştiriciler ve sistem mimarları için teknik derinlik sağlar.

---

## **📌 İçindekiler**
1. [Giriş](#giriş)
2. [Mikroservis Mimarisi Nedir?](#mikroservis-mimarisi-nedir)
    - [Mikroservislerin Temel Özellikleri](#mikroservislerin-temel-özellikleri)
    - [Mikroservis Mimarisi Bileşenleri](#mikroservis-mimarisi-bileşenleri)
3. [En Yaygın Kullanılan Design Pattern’ler](#en-yaygın-kullanılan-design-patternler)
    - [API Gateway Pattern](#api-gateway-pattern)
    - [Service Discovery Pattern](#service-discovery-pattern)
    - [Circuit Breaker Pattern](#circuit-breaker-pattern)
    - [Database per Service Pattern](#database-per-service-pattern)
    - [Saga Pattern](#saga-pattern)
    - [Strangler Fig Pattern](#strangler-fig-pattern)
4. [Kod Örnekleri ve Senaryolar](#kod-örnekleri-ve-senaryolar)
5. [En İyi Uygulamalar](#en-iyi-uygulamalar)
6. [Sonuç ve Kaynaklar](#sonuç-ve-kaynaklar)

---

## **1. Giriş**

Mikroservis mimarisi, büyük ve karmaşık yazılım sistemlerini küçük, bağımsız olarak yönetilebilen hizmetlere (servislere) bölerek daha kolay ölçeklenebilir ve yönetilebilir hale getiren bir yazılım mimarisi yaklaşımıdır.

---

## **2. Mikroservis Mimarisi Nedir?**

Mikroservis mimarisi, uygulamaların tek bir büyük kod yığını yerine bağımsız servisler olarak geliştirilmesini önerir. Her servis kendi veri tabanına ve iş mantığına sahip olabilir ve genellikle API tabanlı olarak iletişim kurar.

### **Mikroservislerin Temel Özellikleri**

| **Özellik**                | **Açıklama**                              |
|----------------------------|---------------------------------------------|
| Bağımsız Dağıtım           | Her servis ayrı ayrı dağıtılabilir.      |
| Esneklik ve Ölçeklenebilirlik | Servisler farklı platformlarda çalışabilir. |
| Tek Sorumluluk İlkesi      | Her mikroservis belirli bir işlevden sorumludur. |
| Veri Yalıtımı              | Her servis kendi veri tabanına sahip olabilir. |
| Bağımsız Geliştirme        | Ekipler farklı servislerde eş zamanlı çalışabilir. |
| Teknoloji Esnekliği        | Servisler farklı dillerde geliştirilebilir. |

### **Mikroservis Mimarisi Bileşenleri**

| **Bileşen**        | **Açıklama**                        |
|--------------------|-------------------------------------|
| API Gateway        | Servisler arası gelen istekleri yönetir. |
| Service Registry   | Servislerin adreslerini depolar.   |
| Load Balancer      | Yük dengelemesi yapar.             |
| Message Broker     | Mesajların asenkron teslimini sağlar. |
| Service Mesh       | Servisler arası trafiği yönetir.   |
| Monitoring & Logging | Uygulama izleme ve hata ayıklama sağlar. |

---

## **3. En Yaygın Kullanılan Design Pattern’ler**

### **1. API Gateway Pattern**

**Amaç:** Tek bir giriş noktası oluşturarak istemcilerin arka uç servislerle doğrudan etkileşime geçmesini engeller.

**Kullanım Senaryoları:** Karmaşık iş mantığına sahip çok sayıda mikroservisin olduğu sistemlerde.

**Kod Örneği:**

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllerRoute(
        name: "api-gateway",
        pattern: "{controller=Home}/{action=Index}/{id?}");
});
```

### **2. Service Discovery Pattern**

**Amaç:** Servislerin IP adreslerini ve portlarını otomatik olarak bulmasını sağlar.

**Kullanım Senaryoları:** Servisler dinamik olarak dağıtıldığında veya ölçeklendirildiğinde.

**Kod Örneği:**

```json
{
  "ServiceRegistry": {
    "url": "http://localhost:8761",
    "register": true
  }
}
```

### **3. Circuit Breaker Pattern**

**Amaç:** Servis hatalarını izleyerek hata oluştuğunda alternatif bir yol sunar veya isteği durdurur.

**Kullanım Senaryoları:** Hata toleransı yüksek sistemlerde.

**Kod Örneği:**

```csharp
services.AddHttpClient("OrderService", client =>
{
    client.BaseAddress = new Uri("https://orderservice/api");
}).AddPolicyHandler(Policy.TimeoutAsync<HttpResponseMessage>(TimeSpan.FromSeconds(10)))
  .AddPolicyHandler(Policy.CircuitBreakerAsync<HttpResponseMessage>(2, TimeSpan.FromSeconds(30)));
```

### **4. Database per Service Pattern**

**Amaç:** Her mikroservisin kendi veritabanına sahip olmasını sağlar ve veri bağımsızlığı sunar.

### **5. Saga Pattern**

**Amaç:** Dağıtık işlemleri yönetmek için işlem zincirlerini kullanır.

**Kod Örneği:**

```json
{
  "state": "pending",
  "nextService": "PaymentService"
}
```

### **6. Strangler Fig Pattern**

**Amaç:** Mevcut monolit uygulamaları yavaş yavaş mikroservislere taşır.

---

## **4. Kod Örnekleri ve Senaryolar**

```csharp
[HttpPost("CreateOrder")]
public async Task<IActionResult> CreateOrder([FromBody] OrderDto order)
{
    var orderId = await _orderService.CreateOrderAsync(order);
    return CreatedAtAction(nameof(GetOrder), new { id = orderId }, order);
}
```

---

## **5. En İyi Uygulamalar**

- **Bağlantı Yönetimi:** Service Registry ve Service Mesh kullanın.
- **Hata Yönetimi:** Circuit Breaker, Retry Policy uygulayın.
- **Loglama ve İzleme:** Distributed Tracing kullanarak hataları izleyin.
- **Veri Bağımsızlığı:** Her mikroservise özel veri tabanı kullanın.
- **API Güvenliği:** API Gateway ve JWT kullanarak güvenlik sağlayın.

---

## **6. Sonuç ve Kaynaklar**

Mikroservis mimarisi, büyük uygulamaları küçük, bağımsız servisler olarak organize ederek ölçeklenebilirlik, esneklik ve hata toleransı sunar.

**Kaynaklar:**
- Microsoft Microservices Best Practices
- AWS Microservices Guide

---
