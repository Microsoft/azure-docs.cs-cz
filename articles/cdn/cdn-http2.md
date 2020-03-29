---
title: Podpora HTTP/2 v Azure CDN | Dokumenty společnosti Microsoft
description: Přečtěte si o podpoře HTTP/2 a CDN.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: 703623e3f7f314d87417458f3f9a218dfdf45427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67849978"
---
# <a name="http2-support-in-azure-cdn"></a>Podpora HTTP/2 v Azure CDN

HTTP/2 je hlavní revize http/1.1\. Poskytuje rychlejší výkon webu, kratší dobu odezvy a lepší uživatelské prostředí při zachování známých metod HTTP, stavových kódů a sémantiky. Ačkoli HTTP/2 je navržen pro práci s HTTP a HTTPS, mnoho klientských webových prohlížečů podporuje pouze HTTP/2 přes TLS.

### <a name="http2-benefits"></a>VÝHODY HTTP/2

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

## <a name="enabling-http2-support-in-azure-cdn"></a>Povolení podpory HTTP/2 v Azure CDN

V současné době je podpora HTTP/2 aktivní pro všechny profily Azure CDN. Od zákazníků není vyžadována žádná další akce.

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit výhody protokolu HTTP/2 v akci, podívejte se na [tuto ukázku z akamai](https://http2.akamai.com/demo).

Další informace o protokolu HTTP/2 naleznete v následujících zdrojích:

*   [Domovská stránka specifikace HTTP/2](https://http2.github.io/)
*   [Oficiální OTÁZKY HTTP/2](https://http2.github.io/faq/)
*   [Akamai HTTP/2 informace](https://http2.akamai.com/)

Další informace o dostupných funkcích Azure CDN najdete v tématu [Přehled Azure CDN](https://azure.microsoft.com/documentation/articles/cdn-overview/).