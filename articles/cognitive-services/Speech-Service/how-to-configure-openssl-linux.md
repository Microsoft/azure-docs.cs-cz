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
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81683168"
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
> [!NOTE]
> Také je potřeba poznamenat, že u některých distribucí systému Linux není definována proměnná prostředí TMP nebo TMPDIR. To způsobí, že sada Speech SDK stáhne seznam odvolaných certifikátů (CRL) pokaždé, když neuloží seznam CRL do mezipaměti pro opakované použití, dokud nevyprší. Pro zlepšení výkonu počátečního připojení můžete [vytvořit proměnnou prostředí s názvem TMPDIR a nastavit ji na cestu k vašemu zvolenému dočasnému adresáři.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o sadě Speech SDK](speech-sdk.md)
