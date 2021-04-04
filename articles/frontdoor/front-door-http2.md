---
title: Přední vrátka Azure – podpora HTTP2 | Microsoft Docs
description: Tento článek vám pomůže získat informace o podpoře HTTP/2 v Azure front-dveřích.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 20d45f5966aca3df89e17e03aa6120a4ddf5c5b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91448693"
---
# <a name="http2-support-in-azure-front-door"></a>Podpora HTTP/2 v Azure front-dveřích

V současné době je podpora protokolu HTTP/2 aktivní pro všechny konfigurace front-dveří Azure. Od zákazníků není vyžadována žádná další akce.

HTTP/2 je hlavní revize HTTP/1.1, která vám poskytne rychlejší webový výkon tím, že se zkrátí doba odezvy. HTTP/2 se provádí udržováním známých metod HTTP, stavových kódů a sémantiky pro zlepšení uživatelského prostředí. I když je HTTP/2 navržený tak, aby fungoval s HTTP a HTTPS, mnoho klientských webových prohlížečů podporuje jenom HTTP/2 přes TLS (Transport Layer Security).

> [!NOTE]
> Podpora protokolu HTTP/2 je k dispozici pouze pro požadavky od klientů do front-dveří. Komunikace z předních dveří do back-endu v rámci fondu back-end se stává přes HTTP/1.1. 

### <a name="http2-benefits"></a>Výhody protokolu HTTP/2

Mezi výhody HTTP/2 patří:

*   **Multiplexování a souběžnost**

    Pomocí protokolu HTTP 1,1 vyžaduje více žádostí o prostředky více připojení TCP a každé z nich má k sobě přiřazené nároky na výkon. HTTP/2 umožňuje vyžádání více prostředků v jednom připojení TCP.

*   **Komprese hlaviček**

    Díky komprimaci hlaviček protokolu HTTP pro obsluhované prostředky se čas na lince významně sníží.

*   **Závislosti streamu**

    Závislosti streamu umožňují klientovi označovat serveru, který má prostředky přednost.


## <a name="http2-browser-support"></a>Podpora prohlížeče HTTP/2

Všechny hlavní prohlížeče mají v jejich současných verzích naimplementovaná podpora HTTP/2. Nepodporované prohlížeče se automaticky přestanou vrátit na HTTP/1.1.

|Prohlížeč|Minimální verze|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Další kroky

Další informace o HTTP/2 najdete v následujících zdrojích informací:

- [Domovská stránka specifikace HTTP/2](https://http2.github.io/)
- [Oficiální Nejčastější dotazy k HTTP/2](https://http2.github.io/faq/)
- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
