---
title: Služba front-dveří Azure – podpora HTTP2 | Microsoft Docs
description: Tento článek vám pomůže získat informace o podpoře HTTP/2 ve službě Azure front-dveří.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: c3c1721454c0b3c96071c685a764f34d4fa540b9
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775258"
---
# <a name="http2-support-in-azure-front-door-service"></a>Podpora HTTP/2 ve službě Azure front-dveří

V současné době je podpora protokolu HTTP/2 aktivní pro všechny konfigurace front-dveří. Od zákazníků není vyžadována žádná další akce.

HTTP/2 je hlavní revize HTTP/1.1. Poskytuje rychlejší webový výkon, omezenou dobu odezvy a vylepšené uživatelské prostředí a současně zachovává známé metody HTTP, stavové kódy a sémantiku. I když je protokol HTTP/2 navržený tak, aby fungoval s HTTP a HTTPS, mnoho klientských webových prohlížečů podporuje jenom HTTP/2 přes TLS (Transport Layer Security).

### <a name="http2-benefits"></a>Výhody protokolu HTTP/2

Mezi výhody HTTP/2 patří:

*   **Multiplexování a souběžnost**

    Pomocí protokolu HTTP 1,1 vyžaduje více žádostí o prostředky více připojení TCP a každé z nich má k sobě přiřazené nároky na výkon. HTTP/2 umožňuje vyžádání více prostředků v jednom připojení TCP.

*   **Komprese hlaviček**

    Díky komprimaci hlaviček protokolu HTTP pro obsluhované prostředky se čas na lince významně sníží.

*   **Závislosti streamu**

    Závislosti streamu umožňují klientovi označovat serveru, který má prostředky přednost.


## <a name="http2-browser-support"></a>Podpora prohlížeče HTTP/2

Všechny hlavní prohlížeče mají v jejich současných verzích naimplementovaná podpora HTTP/2. Nepodporované prohlížeče se automaticky přenemají na HTTP/1.1.

|Browser|Minimální verze|
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
- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
