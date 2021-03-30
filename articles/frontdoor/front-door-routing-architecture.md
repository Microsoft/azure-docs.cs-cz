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
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449193"
---
# <a name="routing-architecture-overview"></a>Přehled architektury směrování

Když přední dveře Azure obdrží požadavky klientů, provede se jedna z těchto dvou věcí. Pokud povolíte ukládání do mezipaměti nebo je předáte příslušnému back-endu aplikace jako reverzní proxy server, odpovězte na ně.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Výběr prostředí front-dveří pro směrování provozu (libovolného vysílání)

Provoz směrovaný do prostředí front-end pro Azure používá pro provoz DNS (Domain Name System) i protokol HTTP (Hypertext Transfer Protocol) [všesměrové vysílání](https://en.wikipedia.org/wiki/Anycast) , které umožňuje, aby si požadavky uživatelů dosáhli nejbližšího prostředí v nejmenším počtu směrování sítě. Tato architektura nabízí lepší dobu odezvy koncových uživatelů tím, že maximalizuje výhody rozděleného protokolu TCP. Přední dvířka uspořádávají svoje prostředí do primárních a záložních "okruhů". Vnější prstenec má prostředí, která jsou blíže uživatelům, a nabízí nižší latence.  Vnitřní prstenec obsahuje prostředí, která můžou zpracovávat převzetí služeb při selhání pro vnější kruhové prostředí pro případ problémů. Vnější prstenec je upřednostňovaným cílem pro veškerý provoz a vnitřní prstenec, který zpracovává přetečení provozu z vnějšího prstence. Každý hostitel nebo doména front-endu, které jsou obsluhovány přes dvířka, se přiřadí primární VIP (virtuální Internet Protocol adresy), které jsou oznámeny prostředími v vnitřním i vnějším prstenci. Záložní virtuální IP adresa je oznámena pouze v prostředích vnitřního prstence. 

Tato architektura zajišťuje, aby žádosti od koncových uživatelů vždy dosáhly nejbližšího prostředí front-endu. I v případě, že preferované prostředí front-dveří není v pořádku, veškerý provoz se automaticky přesune do dalšího nejbližšího prostředí.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Připojení k prostředí front-end (rozdělený protokol TCP)

[Rozdělený protokol TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) je způsob, jak snížit latence a problémy s protokolem TCP tím, že dojde k přerušení připojení, které by znamenalo vysokou dobu odezvy na menší části. U front-endové prostředí přiblížení koncovým uživatelům se připojení TCP ukončí v prostředí front-endu. Připojení TCP, které má v back-endu aplikace velký čas odezvy (RTT), se rozdělí do dvou samostatných připojení. "Krátkým připojením" mezi koncovým uživatelem a front-endové prostředí znamená, že se připojení navázalo více než tři krátké zpáteční operace místo tří dlouhých přenosů, což vede k úsporám latence. "Dlouhé připojení" mezi předním prostředím a back-endu může být předem navázáno a pak znovu použito u ostatních koncových uživatelů požaduje uložení času připojení. Při navazování připojení SSL/TLS (Transport Layer Security) dojde k vynásobení vlivu na rozdělený protokol TCP, protože k zabezpečení připojení je k dispozici více cyklických cest.

## <a name="processing-request-to-match-a-routing-rule"></a>Zpracovává se žádost, aby odpovídala pravidlu směrování.
Po navázání připojení a dokončení metody handshake TLS je prvním krokem po vyžádání žádosti v prostředí front-in, aby se shodoval s pravidlem směrování. Porovnávání je určeno konfiguracemi na předních dveřích, na které konkrétní pravidlo směrování odpovídá požadavku. Přečtěte si informace o tom, jak přední dvířka [přesměrují směrování](front-door-route-matching.md) .

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Určení dostupných back-endu ve fondu back-end pro pravidlo směrování
Jakmile se přední dveře shodují s pravidlem směrování pro příchozí požadavek, je dalším krokem získání stavu sondy stavu pro back-end fond přidružený k pravidlu směrování, pokud není ukládání do mezipaměti. Přečtěte si o tom, jak přední dveře monitorují stav back-endu s využitím [sond stavu](front-door-health-probes.md) pro další informace.

## <a name="forwarding-the-request-to-your-application-backend"></a>Předání žádosti do back-endu aplikace
Za předpokladu, že ukládání do mezipaměti není nakonfigurováno, je požadavek uživatele předán do "nejlepšího" back-endu na základě vaší konfigurace [metody směrování](front-door-routing-methods.md) .

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
