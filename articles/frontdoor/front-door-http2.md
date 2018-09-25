---
title: Služba Azure branou – podpora HTTP2 | Dokumentace Microsoftu
description: Tento článek vám pomůže se naučit týkající se podpory HTTP/2 branou služby Azure
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
ms.openlocfilehash: 4282c9e9b660476992ba6f948bc5e408e9b064a5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968607"
---
# <a name="http2-support-in-azure-front-door-service"></a>Podpora HTTP/2 branou služby Azure
HTTP/2 je hlavní revizi HTTP/1.1. To zajišťuje rychlejší webového výkonu, snížení odezvu a vylepšené uživatelské prostředí a přitom zkušenosti metody HTTP, stavové kódy a sémantika. I když HTTP/2 je navržen pro práci s HTTP a HTTPS, mnoha webových prohlížečích klienta podporují jenom HTTP/2 přes zabezpečení TLS (Transport Layer).

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

## <a name="enabling-http2-support-in-azure-front-door-service"></a>Povolení podpory HTTP/2 branou služby Azure

Podpora HTTP/2 je v současné době aktivní u všech konfigurací s branou. Není žádná další akce vyžaduje od zákazníků.

## <a name="next-steps"></a>Další kroky

Další informace o HTTP/2 najdete na následujících odkazech:

- [Domovská stránka specifikaci protokolu HTTP/2](https://http2.github.io/)
- [Oficiální HTTP/2 – nejčastější dotazy](https://http2.github.io/faq/)
- Zjistěte, jak [vytvořit přední dveře](quickstart-create-front-door.md).
- Přečtěte si [fungování branou](front-door-routing-architecture.md).
