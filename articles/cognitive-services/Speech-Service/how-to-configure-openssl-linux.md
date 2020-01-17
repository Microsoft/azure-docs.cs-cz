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
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156484"
---
# <a name="configure-openssl-for-linux"></a>Konfigurace OpenSSL pro Linux

Při použití libovolné verze sady Speech SDK před 1.9.0 je [OpenSSL](https://www.openssl.org) dynamicky nakonfigurovaný na verzi hostitelského systému. V novějších verzích sady Speech SDK je OpenSSL (verze [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) staticky propojena se základní knihovnou sady Speech SDK.

## <a name="troubleshoot-connectivity"></a>Řešení potíží s připojením

Pokud při použití verze sady Speech SDK pro 1.9.0 dojde k selhání připojení, zajistěte, aby existoval adresář `ssl/certs` v adresáři `/usr/lib`, který se nachází v systému souborů Linux. Pokud `ssl/certs` adresář *neexistuje*, ověřte, zda je ve vašem systému nainstalovaný OpenSSL, a to pomocí následujícího příkazu:

```bash
which openssl
```

Pak vyhledejte adresář OpenSSL `certs` a zkopírujte obsah tohoto adresáře do adresáře `/usr/lib/ssl/certs`. Potom zkuste znovu zjistit, jestli byly problémy s připojením vyřešené.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Sada Speech SDK](speech-sdk.md)