---
title: Podpora protokolu HTTP nebo 2 v Azure CDN | Microsoft Docs
description: Další informace o podpoře protokolu HTTP/2 a CDN.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: rli
ms.openlocfilehash: b4751320af82a29fb13dc6012c1b197ebc2b1f9b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="http2-support-in-azure-cdn"></a>Podpora protokolu HTTP nebo 2 v Azure CDN

HTTP/2 je hlavní revizi k HTTP/1.1\. Nabízí rychlejší webové výkon, sníženou odezvu a vylepšené uživatelské prostředí, při zachování známé metody HTTP, stavové kódy a sémantiku. I když HTTP/2 je navržený na práci s protokoly HTTP a HTTPS, mnoha webových prohlížečích klienta přes protokol TLS podporují pouze HTTP/2.

### <a name="http2-benefits"></a>Výhody HTTP/2

Příklady výhod HTTP/2:

*   **Multiplexní a souběžnost**

    Pomocí protokolu HTTP 1.1, provádění více požadavků prostředků vyžaduje více připojení TCP a každé připojení má zatížení s ním spojená. HTTP/2 umožňuje více prostředky vyžadované na jednoho připojení TCP.

*   **Komprese záhlaví**

    Komprimací hlavičky protokolu HTTP pro obsloužit prostředky, se výrazně snižuje čas v drátové síti.

*   **Datový proud závislosti**

    Datový proud závislosti povolit klientovi pokyn k serveru prostředky, ke kterým mají prioritu.


## <a name="http2-browser-support"></a>Podpora prohlížeče HTTP/2

Všechny hlavní prohlížeče v jejich aktuální verze implementovat podporu protokolu HTTP nebo 2. Nepodporovaná prohlížeče automaticky záložnímu HTTP/1.1.

|Prohlížeč|Minimální verze|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Povolení podpory protokolu HTTP nebo 2 v Azure CDN

Podpora protokolu HTTP/2 je v současné době aktivní pro všechny profily Azure CDN. Žádná další akce je vyžadována od zákazníků.

## <a name="next-steps"></a>Další kroky

Výhody HTTP/2 v praxi, najdete v sekci [tuto ukázku společnosti Akamai](https://http2.akamai.com/demo).

Další informace o protokolu HTTP nebo 2, najdete v následujících zdrojích informací:

*   [Specifikace protokolu HTTP/2 domovské stránky](https://http2.github.io/)
*   [Oficiální HTTP/2 – nejčastější dotazy](https://http2.github.io/faq/)
*   [Informace o Akamai HTTP/2](https://http2.akamai.com/)

Další informace o dostupných funkcí Azure CDN najdete v tématu [přehled CDN Azure](https://azure.microsoft.com/documentation/articles/cdn-overview/).