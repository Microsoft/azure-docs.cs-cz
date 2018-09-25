---
title: Služba Azure branou – směrování architektura | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit globální zobrazení aspekt přední dveře architektury.
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
ms.openlocfilehash: 42a9c4f9983a68459351fc98fb8b757759e585bf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964875"
---
# <a name="routing-architecture-overview"></a>Přehled směrování architektury

Služby Azure branou, když obdrží klient požádá pak buď odpovědi je (Pokud je povoleno ukládání do mezipaměti) a předává je do back-endu aplikace (jako reverzní proxy server).

</br>Existují příležitosti k optimalizaci provoz při směrování do Azure branou také při směrování do back-EndY.

## <a name = "anycast"></a>Výběr prostředí branou provoz směrování (Anycast)

Směrování na branou Azure využívá prostředí [Anycast](https://en.wikipedia.org/wiki/Anycast) DNS (Domain Name System) a provoz protokolu HTTP (Hypertext Transfer Protocol), takže uživatelského provozu půjdou na nejbližší prostředí z hlediska topologie sítě (nejmenším počtem směrování). Tato architektura obvykle nabízí lepší odezvy časy pro koncové uživatele (maximalizace výhod rozdělení TCP). Přední dveře slouží k uspořádání své prostředí do primárního a záložního "okruhy".  Vnější prstenec nemá prostředí, které jsou blíž k uživatelům nabízí nižší latenci.  Vnitřní prstenec má prostředí, která dokáže zpracovat převzetí služeb při selhání pro prostředí vnější prstenec v případě, že dojde k problému. Vnější prstenec je upřednostňovaným cílem, veškerý provoz, ale je potřeba zpracovat přetečení provozu od vnějšího prstence vnitřní prstenec. Z hlediska virtuální IP adresy (virtuální IP adresy) je přiřazen každého hostitele front-endu nebo doménu obsluhuje branou primární virtuální IP adresy, které oznamují prostředí vnitřní a vnější prstenec, stejně jako záložní virtuální IP adresu, která je jenom oznamovaný prostředí vnitřní prstenec. 

</br>Tato celkové strategie zajišťuje, že žádosti od koncových uživatelů vždy oslovit co nejblíže branou prostředí a, že i v případě, že upřednostňovaný branou prostředí není v pořádku pak provozu automaticky přesune do dalšího nejbližší prostředí.

## <a name = "splittcp"></a>Připojování k prostředí branou (rozdělení TCP)

[Rozdělení TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) je technika ke snížení latence a TCP problémy tím, že rozkládají připojení, které by se vám účtovat vysokou dobu odezvy na menší části.  Umístěním prostředí branou blíže koncovým uživatelům a ukončuje připojení protokolu TCP v rámci prostředí branou, je jedno připojení TCP se velké round-trip doba k back-endu aplikace rozdělit na dvě připojení TCP. Krátký připojení mezi koncovým uživatelem a branou prostředí znamená, že získá připojení více než tři krátký výměny zpráv místo tří dlouhé výměny zpráv ukládá latence.  Dlouhé připojení mezi branou prostředí a back-endu můžete předem vytvořit a opětovně použít napříč více volání koncového uživatele, znovu ukládání doba připojení protokolu TCP.  Účinek se násobí při navazování připojení SSL/TLS (Transport Layer Security), protože existují další zpátečních cest k zabezpečení připojení.

## <a name="processing-request-to-match-a-routing-rule"></a>Zpracování požadavku tak, aby odpovídaly pravidlo směrování
Po navázání připojení a provádění SSL metodou handshake, když požadavek pojmenováváme v prostředí s branou odpovídající pravidlo směrování je prvním krokem. Tuto shodu v podstatě je určení ze v popředí všech konfigurací dveří, které konkrétní směrování pravidlo tak, aby odpovídaly požadavku. Přečtěte si, jak přední dveře [trasy odpovídající](front-door-route-matching.md) Další informace.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identifikace k dispozici back-endů v back-endový fond pro pravidlo směrování
Jakmile branou má odpovídající pravidlo směrování na základě příchozího požadavku, a pokud neexistuje žádné ukládání do mezipaměti, dalším krokem je přetahování sondy stavu pro back-endový fond spojené s odpovídající trasy. Přečtěte si, jak přední dveře monitoruje back-end pomocí stavu [sondy stavu](front-door-health-probes.md) Další informace.

## <a name="forwarding-the-request-to-your-application-backend"></a>Předání požadavku do back-endu vaší aplikace
A konečně, za předpokladu, že neexistuje žádné ukládání do mezipaměti nakonfigurovaná, požadavek uživatele je předán "doporučené" back-endu na základě vašich [metody směrování s branou](front-door-routing-methods.md) konfigurace.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořit přední dveře](quickstart-create-front-door.md).
