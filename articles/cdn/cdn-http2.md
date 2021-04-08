---
title: Podpora HTTP/2 v Azure CDN | Microsoft Docs
description: Azure Content Delivery Network podporuje HTTP/2, které mají výhody oproti HTTP/1, jako je například multiplexování & souběžnosti, komprese hlaviček a závislosti streamu.
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
ms.openlocfilehash: d4ed04185da251d9042f6b54a192d0e49ef20ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778537"
---
# <a name="http2-support-in-azure-cdn"></a>Podpora HTTP/2 v Azure CDN

HTTP/2 je hlavní revize HTTP/1.1 \. Poskytuje rychlejší webový výkon, omezenou dobu odezvy a vylepšené uživatelské prostředí a současně zachovává známé metody HTTP, stavové kódy a sémantiku. I když je protokol HTTP/2 navržený tak, aby fungoval s HTTP a HTTPS, mnoho klientských webových prohlížečů podporuje jenom HTTP/2 přes TLS.

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

|Prohlížeč|Minimální verze|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Povolení podpory HTTP/2 v Azure CDN

V současné době je podpora protokolu HTTP/2 aktivní pro všechny profily Azure CDN. Od zákazníků není vyžadována žádná další akce.

## <a name="next-steps"></a>Další kroky

Pokud chcete zobrazit výhody HTTP/2 v akci, podívejte se na [tuto ukázku z Akamai](https://http2.akamai.com/demo).

Další informace o HTTP/2 najdete v následujících zdrojích informací:

*   [Domovská stránka specifikace HTTP/2](https://http2.github.io/)
*   [Oficiální Nejčastější dotazy k HTTP/2](https://http2.github.io/faq/)
*   [Akamai HTTP/2 – informace](https://http2.akamai.com/)

Další informace o funkcích, které jsou k dispozici Azure CDN, najdete v [přehledu Azure CDN](./cdn-overview.md).