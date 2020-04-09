---
title: Azure Front Door – architektura směrování | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže pochopit globální pohled aspekt architektury Front Door.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: a088e52f742f96a13ba61969c2d7a6697c96b145
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879288"
---
# <a name="routing-architecture-overview"></a>Přehled architektury směrování

Azure Front Door, když obdrží vaše požadavky klienta pak buď odpovědi na ně (pokud ukládání do mezipaměti je povoleno) nebo je předá do příslušného back-endu aplikace (jako reverzní proxy server).

</br>Existují příležitosti k optimalizaci provozu při směrování na Azure Front Door, stejně jako při směrování do back-endů.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Výběr prostředí Přední dveře pro směrování provozu (Anycast)

Směrování do prostředí Azure Front Door využívá [Anycast](https://en.wikipedia.org/wiki/Anycast) pro provoz DNS (Domain Name System) a HTTP (Hypertext Transfer Protocol), takže provoz uživatelů přejde do nejbližšího prostředí z hlediska topologie sítě (nejmenší počet směrování). Tato architektura obvykle nabízí lepší časy odezvy pro koncové uživatele (maximalizace výhod split tcp). Přední dveře organizují své prostředí do primárních a záložních "kroužků".  Vnější kroužek má prostředí, která jsou blíže k uživatelům a nabízejí nižší latenci.  Vnitřní kroužek má prostředí, které může zpracovat převzetí služeb při selhání pro prostředí vnějšího kroužku v případě, že dojde k problému. Vnější kroužek je preferovaným cílem pro veškerý provoz, ale vnitřní kroužek je nezbytný pro zvládnutí přetečení provozu z vnějšího kroužku. Pokud jde o VIP (Virtual Internet Protocol addresses), každému hostiteli frontendu nebo doméně obsluhované front door je přiřazenprimární VIP, který je oznamován prostředím ve vnitřním i vnějším kroužku, stejně jako záložní VIP, který je oznamován pouze prostředími ve vnitřním kruhu. 

</br>Tato celková strategie zajišťuje, že požadavky od koncových uživatelů vždy dosáhnou nejbližšího prostředí předních dveří a že i když je upřednostňované prostředí Front Door není v pořádku, provoz se automaticky přesune do nejbližšího prostředí.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Připojení k prostředí předních dveří (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) je technika ke snížení latence a tcp problémy tím, že dojde k přerušení připojení, které by vznikly vysoké round-trip čas na menší kousky.  Umístěním prostředí front door blíže koncovým uživatelům a ukončení mn. Krátké spojení mezi koncovým uživatelem a prostředím front door znamená, že připojení získá navázáno přes tři krátké zpáteční lety namísto tří dlouhých zpátečních cest, což šetří latenci.  Dlouhé připojení mezi prostředím Přední dveře a back-endem lze předem navázat a znovu použít přes více volání koncových uživatelů, což opět šetří čas připojení TCP.  Efekt se násobí při navazování připojení SSL/TLS (Transport Layer Security), protože existuje více zpátečních cest k zabezpečení připojení.

## <a name="processing-request-to-match-a-routing-rule"></a>Zpracování požadavku tak, aby odpovídal pravidlu směrování
Po navázání připojení a provedení protokolu TLS handshake, když požadavek přistane v prostředí front door, odpovídající pravidlo směrování je prvním krokem. Tato shoda v podstatě určuje ze všech konfigurací v předních dveřích, které konkrétní pravidlo směrování odpovídá požadavku. Přečtěte si o tom, jak front door dělá [odpovídající trasy](front-door-route-matching.md) se dozvědět více.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identifikace dostupných back-endů v back-endovém fondu pro pravidlo směrování
Jakmile přední dveře má shoda pro pravidlo směrování na základě příchozí požadavek a pokud není ukládání do mezipaměti, pak dalším krokem je vyžádat stav sondy stavu pro fond back-endu přidružené s odpovídající trasy. Přečtěte si o tom, jak front door monitoruje stav back-endu pomocí [sond stavu,](front-door-health-probes.md) abyste se dozvěděli více.

## <a name="forwarding-the-request-to-your-application-backend"></a>Předání požadavku back-endu aplikace
Nakonec za předpokladu, že není nakonfigurováno ukládání do mezipaměti, je požadavek uživatele předán "nejlepší" back-end na základě konfigurace [metody směrování předních dveří.](front-door-routing-methods.md)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
