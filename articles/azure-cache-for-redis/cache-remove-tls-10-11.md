---
title: Odebrání použití TLS 1,0 a 1,1 s mezipamětí Azure pro Redis | Microsoft Docs
description: Zjistěte, jak z aplikace odebrat TLS 1,0 a 1,1 při komunikaci s Azure cache pro Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: d1d44467d310b87d306ea7401e66784d684a1bf3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901895"
---
# <a name="remove-use-of-tls-10-and-11-with-azure-cache-for-redis"></a>Odebrání použití TLS 1,0 a 1,1 s mezipamětí Azure pro Redis

K použití výhradně TLS 1,2 nebo vyšší úrovně je v celém oboru nabízená oznámení. Verze TLS 1,0 a 1,1 se označují jako náchylné k útokům, jako jsou TOUCHDOWN a POODLE, a obsahují i další slabé stránky ohrožení zabezpečení a ohrožení zabezpečení (CVE). Nepodporují také moderní šifrovací metody a šifrovací sady Doporučené standardy dodržování předpisů PCI. Tento [blog o zabezpečení TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) podrobněji popisuje některé z těchto chyb zabezpečení.

I když žádné z těchto možností nepředstavují bezprostřední problémy, měli byste zvážit přesunutí mimo použití TLS 1,0 a 1,1 jako nejdřívějšího možného využití. Azure cache pro Redis přestane podporovat tyto verze TLS od 31. března 2020. Aby vaše aplikace po tomto datu komunikovala s mezipamětí, budete muset použít aspoň TLS 1,2.

Tento článek poskytuje obecné pokyny k tomu, jak tyto závislosti detekovat a odstranit z vaší aplikace.

## <a name="check-if-your-application-is-already-compliant"></a>Ověřte, jestli je vaše aplikace už kompatibilní.

Nejjednodušší způsob, jak zjistit, jestli vaše aplikace bude fungovat s TLS 1,2, je nastavit minimální verzi protokolu TLS pro testovací nebo pracovní mezipaměť, kterou používá pro TLS 1,2. Nastavení minimální verze protokolu TLS můžete najít v části [Rozšířená nastavení](cache-configure.md#advanced-settings) instance mezipaměti v Azure Portal. Pokud aplikace i nadále funguje podle očekávání po této změně, bude pravděpodobně kompatibilní. Některé klientské knihovny Redis používané v naší aplikaci může být potřeba specificky nakonfigurovat tak, aby povolovaly TLS 1,2, aby se mohly připojit k Azure cache pro Redis prostřednictvím tohoto protokolu zabezpečení.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurace aplikace tak, aby používala TLS 1,2

Většina aplikací využívá klientské knihovny Redis ke zpracování komunikace s jejich mezipamětí. Níže najdete pokyny ke konfiguraci některých oblíbených klientských knihoven v různých programovacích jazycích a architekturách pro použití TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Redis klienti .NET používají nejnižší verzi TLS ve výchozím nastavení v .NET Framework 4.5.2 nebo nižší a nejvyšší verzi TLS 4,6 nebo vyšší. Pokud používáte starší verzi .NET Framework, můžete povolit TLS 1,2 ručně:

* StackExchange. Redis: nastavte `ssl=true` a `sslprotocls=tls12` v připojovacím řetězci.
* ServiceStack. Redis: postupujte podle [těchto pokynů](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Redis klienti .NET Core ve výchozím nastavení používají nejvyšší verzi TLS.

### <a name="java"></a>Java

Redis klienti Java používají protokol TLS 1,0 v Java verze 6 nebo nižší. Jedis, Lettuce a Radisson se nebudou moct připojit ke službě Azure cache pro Redis, pokud je v mezipaměti zakázané TLS 1,0. V současné době neexistuje žádné známé alternativní řešení.

V jazyce Java 7 nebo novějším Redis klienti standardně nepoužívají TLS 1,2, ale můžou být pro něj nakonfigurované. Lettuce a Radisson tuto chvíli nepodporují. Přeruší se, pokud mezipaměť akceptuje jenom připojení TLS 1,2. Jedis umožňuje zadat základní nastavení TLS s následujícím fragmentem kódu:

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

Predis v PHP 7 nefunguje, protože ta podporuje jenom TLS 1,0. V PHP 7.2.1 nebo níže používá Predis standardně TLS 1,0 nebo 1,1. Při vytváření instancí klienta můžete zadat TLS 1,2:

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
