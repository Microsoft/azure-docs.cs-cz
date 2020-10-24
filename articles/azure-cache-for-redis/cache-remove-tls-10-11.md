---
title: Odeberte TLS 1,0 a 1,1 pro použití s Azure cache pro Redis.
description: Zjistěte, jak z aplikace odebrat TLS 1,0 a 1,1 při komunikaci s Azure cache pro Redis.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 7be987b99c60185647ab976691d42b72236c6364
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92496060"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Odeberte TLS 1,0 a 1,1 pro použití s Azure cache pro Redis.

K exkluzivnímu používání protokolu TLS (Transport Layer Security) verze 1,2 nebo novější se nabízí celé odvětví. Je známo, že TLS verze 1,0 a 1,1 jsou náchylné k útokům, jako je TOUCHDOWN a POODLE, a k dalším běžným slabým místám zabezpečení a ohrožením (CVE). Nepodporují také moderní šifrovací metody a šifrovací sady Doporučené standardy dodržování předpisů v oboru platebních karet (PCI). Tento [blog o zabezpečení TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) podrobněji popisuje některé z těchto ohrožení zabezpečení.

V rámci tohoto úsilí budeme provádět následující změny v mezipaměti Azure pro Redis:

* **Fáze 1:** Nastavíme výchozí minimální verzi TLS na 1,2 pro nově vytvořené instance mezipaměti (dříve byla TLS 1,0). Existující instance mezipaměti se v tomto okamžiku neaktualizují. V případě potřeby můžete použít Azure Portal nebo jiná rozhraní API pro správu ke [změně minimální verze protokolu TLS](cache-configure.md#access-ports) na 1,0 nebo 1,1 na zpětnou kompatibilitu.
* **Fáze 2:** Zastavíme podporu TLS 1,1 a TLS 1,0. Po této změně musí vaše aplikace používat protokol TLS 1,2 nebo novější ke komunikaci s mezipamětí. Očekává se, že služba Azure cache for Redis bude k dispozici, i když ji migrujeme tak, aby podporovala pouze TLS 1,2 nebo novější.

  > [!NOTE]
  > Fáze 2 se nezávazně plánuje zahájit od 31. prosince 2020. Důrazně však doporučujeme začít plánovat tuto změnu nyní a aktivně aktualizovat klienty na podporu TLS 1,2 nebo novější. 
  >

V rámci této změny také odebereme podporu pro starší sady šifrováním, které nejsou zabezpečené. Naše podporované sady šifrováním budou omezeny na následující sady, pokud je mezipaměť konfigurována minimálně pomocí protokolu TLS 1,2:

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Tento článek poskytuje obecné pokyny k detekci závislostí na těchto starších verzích TLS a jejich odebrání z aplikace.

Datum, kdy se tyto změny projeví:

| Cloud                | Počáteční datum fáze 1 | Počáteční datum fáze 2         |
|----------------------|--------------------|----------------------------|
| Azure (Global)       |  13. ledna 2020  | Odloženo z důvodu COVID-19  |
| Azure Government     |  13. března 2020    | Odloženo z důvodu COVID-19  |
| Azure (Německo)        |  13. března 2020    | Odloženo z důvodu COVID-19  |
| Azure (Čína) 21Vianet |  13. března 2020    | Odloženo z důvodu COVID-19  |

> [!NOTE]
> Fáze 2 se nezávazně plánuje zahájit od 31. prosince 2020. Tento článek bude aktualizován, pokud jsou nastavena konkrétní data.
>

## <a name="check-whether-your-application-is-already-compliant"></a>Ověřte, zda je aplikace již kompatibilní.

Nejjednodušší způsob, jak zjistit, jestli vaše aplikace bude fungovat s TLS 1,2, je nastavit **minimální hodnotu verze TLS** na TLS 1,2 v testovací nebo pracovní mezipaměti a pak spustit testy. **Minimální verze protokolu TLS** je v [upřesňujících nastaveních](cache-configure.md#advanced-settings) instance mezipaměti v Azure Portal.  Pokud aplikace i nadále funguje podle očekávání po této změně, je to pravděpodobně vyhovující. Možná budete muset nakonfigurovat klientskou knihovnu Redis, kterou vaše aplikace používá, aby povolovala TLS 1,2, aby se mohla připojit k Azure cache pro Redis.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurace aplikace tak, aby používala TLS 1,2

Většina aplikací používá klientské knihovny Redis ke zpracování komunikace s jejich mezipamětí. Tady jsou pokyny pro konfiguraci některých oblíbených klientských knihoven v různých programovacích jazycích a architekturách pro použití TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Redis klienti .NET standardně používají nejstarší verzi TLS ve výchozím nastavení v .NET Framework 4.5.2 nebo starším a používají nejnovější verzi TLS v .NET Framework 4,6 nebo novější. Pokud používáte starší verzi .NET Framework, můžete povolit TLS 1,2 ručně:

* **Stackexchange. Redis:** Nastavte `ssl=true` a `sslprotocols=tls12` v připojovacím řetězci.
* **ServiceStack. Redis:** Postupujte podle pokynů [ServiceStack. Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) a vyžaduje ServiceStack. Redis v 5.6 minimálně.

### <a name="net-core"></a>.NET Core

Redis klienti .NET Core mají výchozí verzi protokolu TLS nastavenou na operační systém, což zjevně závisí na samotném operačním systému. 

V závislosti na verzi operačního systému a všech použitých opravách se může skutečná výchozí verze TLS lišit. I když existuje jeden zdroj informací, je [zde](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) článek pro Windows. 

Pokud ale používáte starý operační systém nebo jste si ho chtěli určitě, doporučujeme nakonfigurovat upřednostňovanou verzi TLS ručně prostřednictvím klienta.


### <a name="java"></a>Java

Redis klienti Java používají TLS 1,0 v Java verze 6 nebo starší. Jedis, Lettuce a Redisson se nemůžou připojit ke službě Azure cache pro Redis, pokud je v mezipaměti zakázané TLS 1,0. Upgradujte rozhraní Java, aby používalo nové verze TLS.

Pro Java 7 nepoužívají klienti Redis ve výchozím nastavení TLS 1,2, ale můžou se pro něj nakonfigurovat. Jedis umožňuje zadat základní nastavení TLS s následujícím fragmentem kódu:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Klienti Lettuce a Redisson ještě nepodporují určení verze protokolu TLS, takže budou přerušit, pokud mezipaměť akceptuje jenom připojení TLS 1,2. Opravy pro tyto klienty se kontrolují, proto si s touto podporou Přečtěte aktualizované verze těchto balíčků.

V jazyce Java 8 se standardně používá TLS 1,2 a ve většině případů nemusí vyžadovat aktualizace konfigurace klienta. Chcete-li být bezpečné, otestujte svoji aplikaci.

### <a name="nodejs"></a>Node.js

Uzly Redis a IORedis ve výchozím nastavení používají TLS 1,2.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* Verze starší než PHP 7: Predis podporuje pouze TLS 1,0. Tyto verze nefungují s TLS 1,2; musíte upgradovat na použití TLS 1,2.
 
* PHP 7,0 až PHP 7.2.1: Predis používá ve výchozím nastavení pouze TLS 1,0 nebo 1,1. K použití TLS 1,2 můžete použít následující alternativní řešení. Zadejte TLS 1,2 při vytváření instance klienta:

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7,3 a novější verze: Predis používá nejnovější verzi TLS.

#### <a name="phpredis"></a>PhpRedis

PhpRedis nepodporuje protokol TLS u žádné verze PHP.

### <a name="python"></a>Python

Redis-py používá standardně TLS 1,2.

### <a name="go"></a>GO

RediGO používá standardně TLS 1,2.

## <a name="additional-information"></a>Další informace

- [Jak nakonfigurovat Azure cache pro Redis](cache-configure.md)
