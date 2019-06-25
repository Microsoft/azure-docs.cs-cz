---
title: Podpora HTTP/2 v Azure CDN | Dokumentace Microsoftu
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
ms.openlocfilehash: 2d27cd54486a08e18fe74c852af29d5cf6432023
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60737070"
---
# <a name="http2-support-in-azure-cdn"></a>Podpora HTTP/2 v Azure CDN

HTTP/2 je hlavní revizi HTTP/1.1\. To zajišťuje rychlejší webového výkonu, snížení odezvu a vylepšené uživatelské prostředí a přitom zkušenosti metody HTTP, stavové kódy a sémantika. I když HTTP/2 je navržen pro práci s HTTP a HTTPS, mnoha webových prohlížečích klienta přes protokol TLS podporují jenom HTTP/2.

### <a name="http2-benefits"></a>Výhody protokolu HTTP/2

Výhody protokolu HTTP/2 patří:

*   **Multiplexingu a souběžnost**

    Pomocí protokolu HTTP 1.1, provedení žádosti více prostředků vyžaduje více připojení TCP a každé připojení má nároky na výkon s ním spojená. HTTP/2 umožňuje více prostředků k požadovat u jednoho připojení TCP.

*   **Komprimaci hlaviček**

    Kompresí hlavičky protokolu HTTP pro obsloužit prostředky je výrazně snížit čas na lince.

*   **Stream závislosti**

    Závislosti Stream povolit klientům sdělit serveru prostředků, které mají prioritu.


## <a name="http2-browser-support"></a>Podpora prohlížeče HTTP/2

Všechny hlavní prohlížeče implementovali podpora HTTP/2 v jejich aktuálních verzí. Nepodporovaná prohlížeče automaticky náhradu za HTTP/1.1.

|Prohlížeč|Minimální verze|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Povolení podpory HTTP/2 v Azure CDN

Podpora HTTP/2 je v současné době aktivní pro všechny profily Azure CDN. Není žádná další akce vyžaduje od zákazníků.

## <a name="next-steps"></a>Další kroky

Výhody protokolu HTTP/2 v akci najdete v tématu [této ukázky od Akamai](https://http2.akamai.com/demo).

Další informace o HTTP/2 najdete na následujících odkazech:

*   [Domovská stránka specifikaci protokolu HTTP/2](https://http2.github.io/)
*   [Oficiální HTTP/2 – nejčastější dotazy](https://http2.github.io/faq/)
*   [Informace o společnosti Akamai HTTP/2](https://http2.akamai.com/)

Další informace o dostupných funkcích Azure CDN, najdete v článku [přehled sítě CDN Azure](https://azure.microsoft.com/documentation/articles/cdn-overview/).