---
title: Odebrání tls 1.0 a 1.1 z použití s Azure Cache pro Redis
description: Zjistěte, jak odebrat TLS 1.0 a 1.1 z vaší aplikace při komunikaci s Azure Cache pro Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 0d28469820f63f63089d9b91d57ccd7fe75c8b95
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348642"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Odebrání tls 1.0 a 1.1 z použití s Azure Cache pro Redis

Existuje celooborový tlak směrem k výhradnímu použití zabezpečení transportní vrstvy (TLS) verze 1.2 nebo novější. Je známo, že tls verze 1.0 a 1.1 jsou náchylné k útokům, jako jsou BEAST a POODLE, a mají další společné chyby zabezpečení a chyby expozice (CVE). Nepodporují také moderní metody šifrování a šifrovací sady doporučené standardy pci (Payment Card Industry). Tento [blog zabezpečení TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) vysvětluje některé z těchto chyb zabezpečení podrobněji.

V rámci tohoto úsilí provedeme následující změny azure cache pro Redis:

* **Fáze 1:** Nakonfigurujeme výchozí minimální verzi TLS na 1.2 pro nově vytvořené instance mezipaměti. (Toto bývalo TLS 1.0.) Existující instance mezipaměti nebudou v tomto okamžiku aktualizovány. V případě potřeby budete moci [změnit minimální verzi TLS](cache-configure.md#access-ports) zpět na 1.0 nebo 1.1 pro zpětnou kompatibilitu. Tuto změnu lze provést prostřednictvím portálu Azure nebo jiných řešení API pro správu.
* **Fáze 2:** Přestaneme podporovat TLS verze 1.0 a 1.1. Po této změně bude aplikace muset ke komunikaci s mezipamětí používat tls 1.2 nebo novější.

Kromě toho v rámci této změny budeme odebírání podpory pro starší, nezabezpečené cypher sady.  Naše podporované cypherové sady budou omezeny na následující, pokud je mezipaměť nakonfigurována s minimální verzí TLS 1.2.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Tento článek obsahuje obecné pokyny o tom, jak zjistit závislosti na těchto starších verzích TLS a odebrat je z vaší aplikace.

Data, kdy se tyto změny projeví, jsou:

| Cloud               | Počáteční datum fáze 1 | Počáteční datum fáze 2      |
|---------------------|--------------------|-------------------------|
| Azure (globální)      |  13. ledna 2020  | 11. května 2020 (prodlouženo) |
| Azure Government    |  13. března 2020    | 11. května 2020            |
| Azure Germany       |  13. března 2020    | 11. května 2020            |
| Azure (Čína)         |  13. března 2020    | 11. května 2020            |

## <a name="check-whether-your-application-is-already-compliant"></a>Zkontrolujte, zda je aplikace již kompatibilní

Nejjednodušší způsob, jak zjistit, zda bude vaše aplikace pracovat s TLS 1.2, je nastavit minimální hodnotu **verze TLS** na TLS 1.2 v testovací nebo pracovní mezipaměti, kterou používá. Minimální nastavení **verze TLS** je v [rozšířenénastavení](cache-configure.md#advanced-settings) instance mezipaměti na webu Azure Portal. Pokud aplikace nadále fungovat podle očekávání po této změně, je pravděpodobně kompatibilní. Možná budete muset nakonfigurovat některé klientské knihovny Redis používané vaší aplikací speciálně k povolení TLS 1.2, aby se mohly připojit k Azure Cache for Redis přes tento protokol zabezpečení.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurace aplikace pro použití protokolu TLS 1.2

Většina aplikací používá klientské knihovny Redis ke zpracování komunikace s jejich mezipamětí. Zde jsou pokyny pro konfiguraci některých populárních klientských knihoven v různých programovacích jazycích a architekturách pro použití TLS 1.2.

### <a name="net-framework"></a>.NET Framework

Klienti Redis .NET používají nejstarší verzi TLS ve výchozím nastavení v rozhraní .NET Framework 4.5.2 nebo starší a používají nejnovější verzi TLS v rozhraní .NET Framework 4.6 nebo novější. Pokud používáte starší verzi rozhraní .NET Framework, můžete tls 1.2 povolit ručně:

* **StackExchange.Redis:** Set `ssl=true` `sslprotocols=tls12` a v připojovacím řetězci.
* **ServiceStack.Redis:** Postupujte podle [pokynů ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Klienti Redis .NET Core používají ve výchozím nastavení nejnovější verzi TLS.

### <a name="java"></a>Java

Klienti Redis Java používají TLS 1.0 na Javě verze 6 nebo starší. Jedis, Salát a Redisson se nemohou připojit k Azure Cache pro Redis, pokud je tls 1.0 v mezipaměti zakázán. Upgradujte java framework tak, aby používal nové verze TLS.

Pro Java 7 klienti Redis ve výchozím nastavení nepoužívají TLS 1.2, ale mohou být pro něj nakonfigurováni. Jedis umožňuje zadat základní nastavení TLS s následujícím fragmentem kódu:

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

Klienti Salad a Redisson zatím nepodporují určení verze TLS, takže se přeruší, pokud mezipaměť přijímá pouze připojení TLS 1.2. Opravy pro tyto klienty jsou kontrolovány, proto zkontrolujte s těmito balíčky pro aktualizovanou verzi s touto podporou.

V jazyce Java 8 se tls 1.2 používá ve výchozím nastavení a ve většině případů by neměl vyžadovat aktualizace konfigurace klienta. Chcete-li být v bezpečí, otestujte aplikaci.

### <a name="nodejs"></a>Node.js

Uzel Redis a IORedis používají ve výchozím nastavení TLS 1.2.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis (Predis)
 
* Verze starší než PHP 7: Predis podporuje pouze TLS 1.0. Tyto verze nefungují s TLS 1.2; chcete-li používat tls 1.2, musíte provést upgrade.
 
* PHP 7.0 až PHP 7.2.1: Predis ve výchozím nastavení používá pouze TLS 1.0 nebo 1.1. K použití tls 1.2 můžete použít následující řešení. Při vytváření instance klienta zadejte TLS 1.2:

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

* PHP 7.3 a novější verze: Predis používá nejnovější verzi TLS.

#### <a name="phpredis"></a>PhpRedis

PhpRedis nepodporuje TLS na žádné verzi PHP.

### <a name="python"></a>Python

Redis-py ve výchozím nastavení používá TLS 1.2.

### <a name="go"></a>GO

Redigo ve výchozím nastavení používá TLS 1.2.

## <a name="additional-information"></a>Další informace

- [Jak nakonfigurovat Azure Cache pro Redis](cache-configure.md)
