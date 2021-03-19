---
title: Jak nakonfigurovat OpenSSL pro Linux
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nakonfigurovat OpenSSL pro Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6225fec30a87ca0bbe57e414733bc21489f87ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577440"
---
# <a name="configure-openssl-for-linux"></a>Konfigurace OpenSSL pro Linux

Při použití libovolné verze sady Speech SDK před 1.9.0 je [OpenSSL](https://www.openssl.org) dynamicky nakonfigurovaný na verzi hostitelského systému. V novějších verzích sady Speech SDK je OpenSSL (verze [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) staticky propojena se základní knihovnou sady Speech SDK.

Aby bylo zajištěno připojení, ověřte, zda byly v systému nainstalovány certifikáty OpenSSL. Spusťte příkaz:
```bash
openssl version -d
```

Výstup v systémech založených na Ubuntu/Debian by měl být následující:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Ověřte, zda je `certs` pod OPENSSLDIR podadresářem. V příkladu výše by to bylo `/usr/lib/ssl/certs` .

* Pokud existuje `/usr/lib/ssl/certs` a obsahuje mnoho jednotlivých souborů certifikátů (s `.crt` `.pem` příponou nebo), není nutné provádět další akce.

* Pokud je OPENSSLDIR něco jiného než `/usr/lib/ssl` a/nebo je k dispozici jeden soubor sady prostředků místo několika individuálních souborů, musíte nastavit příslušnou proměnnou prostředí SSL, která bude označovat, kde se certifikáty mají najít.

## <a name="examples"></a>Příklady

- OPENSSLDIR je `/opt/ssl` . Existuje `certs` podadresář s mnoha `.crt` `.pem` soubory nebo.
Nastavte proměnnou prostředí `SSL_CERT_DIR` tak, aby ukazovala `/opt/ssl/certs` před spuštěním programu, který používá sadu Speech SDK. Například:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR je `/etc/pki/tls` (jako v systémech založených na RHEL/CentOS). Existuje `certs` podadresář se souborem sady certifikátů, například `ca-bundle.crt` .
Nastavte proměnnou prostředí `SSL_CERT_FILE` tak, aby odkazovala na tento soubor před spuštěním programu, který používá sadu Speech SDK. Například:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>Kontroly odvolání certifikátů
Sada Speech SDK při připojování ke službě rozpoznávání řeči ověří, že certifikát TLS používaný službou Speech není odvolán. K provedení této kontroly bude sada Speech SDK potřebovat přístup k distribučním bodům seznamu CRL pro certifikační autority, které používá Azure. V [tomto dokumentu](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes)najdete seznam možných umístění pro stahování seznamu odvolaných certifikátů. Pokud byl certifikát odvolán nebo se seznam CRL nedá stáhnout, sada Speech SDK přeruší připojení a vyvolá stornovanou událost.

V případě, že síť, ve které je sada Speech SDK používána, je konfigurována způsobem, který nepovoluje přístup k umístěním pro stahování seznamu CRL, je možné kontrolu seznamu odvolaných certifikátů zakázat nebo nastavit jako neúspěšné, pokud nelze načíst seznam CRL. Tato konfigurace se provádí pomocí objektu konfigurace, který se používá k vytvoření objektu pro rozpoznávání.

Pokud chcete pokračovat v připojení, když se seznam CRL nedá načíst, nastavte vlastnost OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
Při nastavení na hodnotu "pravda" se provede pokus o načtení seznamu odvolaných certifikátů a v případě úspěšného načtení bude certifikát zkontrolován, pokud se nezdaří načtení, připojení bude moci pokračovat.

Chcete-li zcela zakázat kontrolu odvolaných certifikátů, nastavte vlastnost OPENSSL_DISABLE_CRL_CHECK na hodnotu "pravda".
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> Také je potřeba poznamenat, že u některých distribucí systému Linux není definována proměnná prostředí TMP nebo TMPDIR. To způsobí, že sada Speech SDK stáhne seznam odvolaných certifikátů (CRL) pokaždé, když neuloží seznam CRL do mezipaměti pro opakované použití, dokud nevyprší. Pro zlepšení výkonu počátečního připojení můžete [vytvořit proměnnou prostředí s názvem TMPDIR a nastavit ji na cestu k vašemu zvolenému dočasnému adresáři.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o sadě Speech SDK](speech-sdk.md)
