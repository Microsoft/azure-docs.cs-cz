---
title: Podpora Azure Front Door – HTTP2 | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže dozvědět se o podpoře HTTP/2 v Azure Front Door
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
ms.openlocfilehash: 8a3ae8065553b34a72528cb0f2681e327dc90097
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985180"
---
# <a name="http2-support-in-azure-front-door"></a>Podpora HTTP/2 v Azure Front Door

V současné době je podpora HTTP/2 aktivní pro všechny konfigurace Azure Front Door. Od zákazníků není vyžadována žádná další akce.

HTTP/2 je hlavní revize HTTP/1.1. Poskytuje rychlejší výkon webu, kratší dobu odezvy a lepší uživatelské prostředí při zachování známých metod HTTP, stavových kódů a sémantiky. Přestože http/2 je určen pro práci s HTTP a HTTPS, mnoho klientských webových prohlížečů podporuje pouze HTTP/2 přes zabezpečení transportní vrstvy (TLS).

> [!NOTE]
> Podpora protokolu HTTP/2 je k dispozici pouze pro požadavky klientů na front door. Komunikace z front door do zadní chod v back-end fondu probíhá přes HTTP/1.1. 

### <a name="http2-benefits"></a>Výhody HTTP/2

Výhody HTTP/2 zahrnují:

*   **Multiplexování a souběžnost**

    Pomocí protokolu HTTP 1.1 vyžaduje vytváření více požadavků na prostředky více připojení TCP a každé připojení má s sebou spojené nároky na výkon. Protokol HTTP/2 umožňuje vyžádání více prostředků v jednom připojení TCP.

*   **Komprese záhlaví**

    Komprimací hlavičky HTTP pro obsluhované prostředky se výrazně sníží čas na lince.

*   **Závislosti datového proudu**

    Závislosti datového proudu umožňují klientovi označit serveru, které prostředky mají prioritu.


## <a name="http2-browser-support"></a>Podpora prohlížeče HTTP/2

Všechny hlavní prohlížeče implementovaly podporu HTTP/2 ve svých aktuálních verzích. Nepodporované prohlížeče automaticky záložní na HTTP/1.1.

|Prohlížeč|Minimální verze|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Další kroky

Další informace o protokolu HTTP/2 naleznete v následujících zdrojích:

- [Domovská stránka specifikace HTTP/2](https://http2.github.io/)
- [Oficiální OTÁZKY HTTP/2](https://http2.github.io/faq/)
- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
