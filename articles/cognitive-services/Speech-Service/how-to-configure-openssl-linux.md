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
ms.openlocfilehash: ff8772f7c3c3213c010b0bdbd0d0aa8897404bac
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119994"
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

Ověřte, zda je v OPENSSLDIR podadresář `certs`. V příkladu výše by byl `/usr/lib/ssl/certs`.

* Pokud je `/usr/lib/ssl/certs` a obsahuje mnoho jednotlivých souborů certifikátů (s rozšířením `.crt` nebo `.pem`), není nutné provádět další akce.

* Pokud je OPENSSLDIR jiným způsobem než `/usr/lib/ssl` a/nebo existuje jeden soubor sady prostředků namísto několika individuálních souborů, musíte nastavit příslušnou proměnnou prostředí SSL, která bude označovat, kde se certifikáty mají najít.

## <a name="examples"></a>Příklady

- OPENSSLDIR je `/opt/ssl`. Existuje `certs` podadresář s mnoha soubory `.crt` nebo `.pem`.
Nastavte proměnnou prostředí `SSL_CERT_DIR`, aby odkazovala na `/opt/ssl/certs` před spuštěním programu, který používá sadu Speech SDK. Příklad:
```bash
SSL_CERT_DIR=/opt/ssl/certs ./helloworld
```

- OPENSSLDIR je `/etc/pki/tls`. Existuje soubor sady certifikátů, například `ca-bundle.pem` nebo `ca-bundle.crt`.
Nastavte proměnnou prostředí `SSL_CERT_FILE`, aby odkazovala na `/etc/pki/tls/ca-bundle.pem` před spuštěním programu, který používá sadu Speech SDK. Příklad:
```bash
SSL_CERT_FILE=/etc/pki/tls/ca-bundle.pem ./helloworld
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Sada Speech SDK](speech-sdk.md)
