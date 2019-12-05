---
title: Odeberte TLS 1,0 a 1,1 pro použití s Azure cache pro Redis.
description: Zjistěte, jak z aplikace odebrat TLS 1,0 a 1,1 při komunikaci s Azure cache pro Redis.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 74fcce412b2673a3ec9e4809cef018f1afbc3530
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812839"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Odeberte TLS 1,0 a 1,1 pro použití s Azure cache pro Redis.

K exkluzivnímu používání protokolu TLS (Transport Layer Security) verze 1,2 nebo novější se nabízí celé odvětví. Je známo, že TLS verze 1,0 a 1,1 jsou náchylné k útokům, jako je TOUCHDOWN a POODLE, a k dalším běžným slabým místám zabezpečení a ohrožením (CVE). Nepodporují také moderní šifrovací metody a šifrovací sady Doporučené standardy dodržování předpisů v oboru platebních karet (PCI). Tento [blog o zabezpečení TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) podrobněji popisuje některé z těchto ohrožení zabezpečení.

I když žádný z těchto otázek nepředstavuje okamžitý problém, doporučujeme, abyste zastavili používání protokolu TLS 1,0 a 1,1 brzy. Azure cache pro Redis přestane podporovat tyto verze protokolu TLS od 31. března 2020. Po tomto datu bude vaše aplikace vyžadovat použití TLS 1,2 nebo novější ke komunikaci s mezipamětí.

Tento článek poskytuje obecné pokyny k detekci závislostí na těchto starších verzích TLS a jejich odebrání z aplikace.

## <a name="check-whether-your-application-is-already-compliant"></a>Ověřte, zda je aplikace již kompatibilní.

Nejjednodušší způsob, jak zjistit, jestli vaše aplikace bude fungovat s TLS 1,2, je nastavit **minimální hodnotu verze TLS** na TLS 1,2 v testovací nebo pracovní mezipaměti, kterou používá. **Minimální verze protokolu TLS** je v [upřesňujících nastaveních](cache-configure.md#advanced-settings) instance mezipaměti v Azure Portal. Pokud aplikace i nadále funguje podle očekávání po této změně, je to pravděpodobně vyhovující. Je možné, že budete muset nakonfigurovat některé klientské knihovny Redis, které vaše aplikace používá, konkrétně k povolení TLS 1,2, aby se mohly připojit k mezipaměti Azure pro Redis prostřednictvím tohoto protokolu zabezpečení.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurace aplikace tak, aby používala TLS 1,2

Většina aplikací používá klientské knihovny Redis ke zpracování komunikace s jejich mezipamětí. Tady jsou pokyny pro konfiguraci některých oblíbených klientských knihoven v různých programovacích jazycích a architekturách pro použití TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Redis klienti .NET standardně používají nejstarší verzi TLS ve výchozím nastavení v .NET Framework 4.5.2 nebo starším a používají nejnovější verzi TLS v .NET Framework 4,6 nebo novější. Pokud používáte starší verzi .NET Framework, můžete povolit TLS 1,2 ručně:

* **Stackexchange. Redis:** V připojovacím řetězci nastavte `ssl=true` a `sslprotocols=tls12`.
* **ServiceStack. Redis:** Postupujte podle [pokynů ServiceStack. Redis](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Redis klienti .NET Core používají ve výchozím nastavení nejnovější verzi TLS.

### <a name="java"></a>Java

Redis klienti Java používají TLS 1,0 v Java verze 6 nebo starší. Jedis, Lettuce a Radisson se nemůžou připojit ke službě Azure cache pro Redis, pokud je v mezipaměti zakázané TLS 1,0. V současné době není k dispozici žádné známé alternativní řešení.

V jazyce Java 7 nebo novějším používají klienti Redis ve výchozím nastavení TLS 1,2, ale můžou se pro něj nakonfigurovat. Lettuce a Radisson tuto konfiguraci teď nepodporují. Přeruší se, pokud mezipaměť akceptuje pouze připojení TLS 1,2. Jedis umožňuje zadat základní nastavení TLS s následujícím fragmentem kódu:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

Uzly Redis a IORedis ve výchozím nastavení používají TLS 1,2.

### <a name="php"></a>PHP

Predis v PHP 7 nebude fungovat, protože PHP 7 podporuje jenom TLS 1,0. Ve výchozím nastavení používá Predis ve PHP 7.2.1 nebo starší protokol TLS 1,0 nebo 1,1. Při vytváření instance klienta můžete zadat TLS 1,2:

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

V PHP 7,3 nebo vyšší Predis používá nejnovější verzi TLS.

PhpRedis nepodporuje protokol TLS u žádné verze PHP.

### <a name="python"></a>Python

Redis-py používá standardně TLS 1,2.

### <a name="go"></a>GO

RediGO používá standardně TLS 1,2.

## <a name="additional-information"></a>Další informace

- [Jak nakonfigurovat Azure cache pro Redis](cache-configure.md)
