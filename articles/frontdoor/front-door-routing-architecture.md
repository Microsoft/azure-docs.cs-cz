---
title: Přední vrátka Azure – architektura směrování | Microsoft Docs
description: Tento článek vám pomůže pochopit, jaký je globální aspekt zobrazení architektury předních dveří.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: b36852e27f6aa3a909dd645c19a12c55e082b761
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399323"
---
# <a name="routing-architecture-overview"></a>Přehled architektury směrování

Přední dveře Azure, když obdrží požadavky klientů, na ně odpoví (Pokud je povoleno ukládání do mezipaměti) nebo předají do příslušné aplikace back-end (jako reverzní proxy server).

</br>Při směrování do front Azure a také při směrování do back-endu jsou k dispozici příležitosti pro optimalizaci provozu.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Výběr prostředí front-dveří pro směrování provozu (libovolného vysílání)

Směrování do prostředí front Azure [využívá pro přenos](https://en.wikipedia.org/wiki/Anycast) dat DNS (Domain Name System) i protokol HTTP (Hypertext Transfer Protocol) i příchozí přenos dat, takže přenosy uživatelů budou přejít do nejbližšího prostředí z hlediska síťové topologie (s nejmenším segmentem směrování). Tato architektura obvykle nabízí lepší dobu odezvy pro koncové uživatele (což maximalizuje výhody rozděleného protokolu TCP). Přední dvířka uspořádávají svoje prostředí do primárních a záložních "okruhů".  Vnější prstenec má prostředí, která jsou blíže uživatelům, a nabízí nižší latence.  Vnitřní prstenec obsahuje prostředí, která můžou zpracovávat převzetí služeb při selhání pro vnější kruhové prostředí pro případ, že dojde k problému. Vnější prstenec je upřednostňovaným cílem pro veškerý provoz, ale vnitřní prstenec je nezbytný pro zpracování přetečení provozu z vnějšího prstence. Z hlediska VIP (virtuálních Internet Protocol adres) se každému hostiteli front-endu nebo doméně, která je obsluhována přes dvířka, přiřadí primární VIP, která je oznámená prostředími v interním i vnějším prstenci, a také záložní VIP, která je hlášena pouze v prostředích vnitřního prstence. 

</br>Tato celková strategie zajišťuje, že požadavky od koncových uživatelů mají vždycky přístup k nejbližšímu prostředí front-endu a že i v případě, že upřednostňované prostředí front-endu není v pořádku, provoz se automaticky přesune do dalšího nejbližšího prostředí.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Připojení k prostředí front-end (rozdělený protokol TCP)

[Rozdělený protokol TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) je způsob, jak snížit latence a problémy s protokolem TCP tím, že dojde k přerušení připojení, které by znamenalo vysokou dobu odezvy na menší části.  Po umístění front-endové prostředí blíže koncovým uživatelům a ukončení připojení TCP v prostředí front-endu je jedno připojení TCP s velkou dobou odezvy (RTT) do back-endu aplikace rozděleno na dvě připojení TCP. Krátké připojení mezi koncovým uživatelem a front-endové prostředí znamená, že se připojení navázalo se třemi krátkými zpětnými zpátečními znaky namísto tří dlouhých přenosů, což šetří latenci.  Dlouhé připojení mezi prostředími front-endu a back-end se dá předem zřídit a znovu použít napříč několika voláními koncových uživatelů, takže se znovu uloží doba připojení protokolu TCP.  Tento efekt se vynásobí při vytváření připojení SSL/TLS (Transport Layer Security), protože k zabezpečení připojení je k dispozici více cyklických cest.

## <a name="processing-request-to-match-a-routing-rule"></a>Zpracovává se žádost, aby odpovídala pravidlu směrování.
Po navázání připojení a provedení metody handshake TLS dojde v případě, že je žádost umístěná v prostředí front-dveří, jako je první krok. Tato shoda v podstatě vychází ze všech konfigurací v předních dveřích, které konkrétní pravidlo směrování odpovídá požadavku. Přečtěte si informace o tom, jak přední dvířka [přesměrují směrování](front-door-route-matching.md) .

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Určení dostupných back-endu ve fondu back-end pro pravidlo směrování
Jakmile má dopředné dveře shodu s pravidlem směrování na základě příchozího požadavku a pokud není ukládání do mezipaměti, pak dalším krokem je vyžádat si stav sondy stavu pro back-end fond přidružený k odpovídající trase. Přečtěte si o tom, jak přední dveře monitorují stav back-endu s využitím [sond stavu](front-door-health-probes.md) pro další informace.

## <a name="forwarding-the-request-to-your-application-backend"></a>Předání žádosti do back-endu aplikace
Za předpokladu, že není nakonfigurované žádné ukládání do mezipaměti, požadavek uživatele se přesměruje do "nejlepšího" back-endu na základě konfigurace [metody směrování na přední dveře](front-door-routing-methods.md) .

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
