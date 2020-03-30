---
title: Azure Functions geografické zotavení po havárii a vysoká dostupnost
description: Jak používat geografické oblasti pro redundanci a převzetí služeb při selhání v Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 481a716bd6ced5c304da41c70fdcfc687b76661d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080236"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions geografické zotavení po havárii

Když celé oblasti Azure nebo datová centra dojít k prostojům, je důležité pro výpočetní prostředky pokračovat ve zpracování v jiné oblasti.  Tento článek vysvětluje některé strategie, které můžete použít k nasazení funkcí, které umožňují zotavení po havárii.

## <a name="basic-concepts"></a>Základní koncepty

Funkce Azure běží v určité oblasti.  Chcete-li získat vyšší dostupnost, můžete nasadit stejné funkce do více oblastí.  Ve více oblastech můžete mít své funkce spuštěné v *aktivním/aktivním* vzoru nebo *v aktivním/pasivním* vzoru.  

* Aktivní/aktivní vysoká dostupnost: Obě oblasti jsou aktivní a přijímající události (duplicitní nebo otočení). Aktivní/aktivní se doporučuje pro funkce HTTPS v kombinaci s Azure Front Door.
* Aktivní/pasivní. Jedna oblast je aktivní a přijímá události, zatímco sekundární je nečinný.  Pokud je požadováno převzetí služeb při selhání, sekundární oblast je aktivována a převezme zpracování.  To se doporučuje pro funkce bez protokolu HTTP, jako je Service Bus a Event Hubs.

Další informace o nasazeních ve více oblastech najdou další informace o tom, jak [spouštět aplikace ve více oblastech.](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="activeactive-for-https-functions"></a>Aktivní/aktivní pro funkce HTTPS

K dosažení aktivního nebo aktivního nasazení funkcí vyžaduje některé součásti, které mohou koordinovat události mezi oběma oblastmi.  Pro funkce HTTPS se tato koordinace provádí pomocí [Azure Front Door](../frontdoor/front-door-overview.md).  Azure Front Door můžete směrovat a kruhové dotazování https požadavky mezi více regionálních funkcí.  Také pravidelně kontroluje stav každého koncového bodu.  Pokud regionální funkce přestane reagovat na kontroly stavu, Azure Front Door ji vyvede z rotace a přepošle provoz pouze na funkce v pořádku.  

![Architektura pro přední dveře a funkce Azure](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Aktivní/aktivní pro funkce bez protokolu HTTPS

Stále můžete dosáhnout aktivního/aktivního nasazení pro funkce bez protokolu HTTPS.  Je však třeba zvážit, jak budou tyto dvě oblasti vzájemně spolupracovat nebo koordinovat.  Pokud jste nasadili stejnou aplikaci funkce do dvou oblastí, z nichž každá se aktivuje ve stejné frontě service bus, budou fungovat jako konkurenční spotřebitelé při vyřazení této fronty do fronty.  I když to znamená, že každá zpráva je zpracovávána pouze jednou z instancí, znamená to také, že v jedné sběrnici je stále jediný bod selhání.  Pokud nasadíte dvě fronty service bus (jeden v primární oblasti, jeden v sekundární oblasti) a dvě aplikace funkce ukázal na jejich fronty oblasti, výzva nyní přichází v tom, jak jsou zprávy fronty distribuovány mezi dvěma oblastmi.  Často to znamená, že každý vydavatel se pokusí publikovat zprávu do *obou* oblastí a každá zpráva je zpracována oběma aplikacemi aktivních funkcí.  I když to vytvoří aktivní/aktivní vzor, vytvoří další problémy kolem duplikace výpočetních prostředků a kdy nebo jak jsou data konsolidována.  Z těchto důvodů se doporučuje pro aktivační události bez https používat aktivní/pasivní vzor.

## <a name="activepassive-for-non-https-functions"></a>Aktivní/pasivní pro funkce bez PROTOKOLU HTTPS

Aktivní/pasivní poskytuje způsob, jak pouze jednu funkci zpracovat každou zprávu, ale poskytuje mechanismus převzetí služeb při selhání do sekundární oblasti v případě havárie.  Azure Functions funguje společně s [geografickou obnovou Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md) a [geografickou obnovou Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md).

Použití Azure Event Hubs aktivační události jako příklad, aktivní/pasivní vzor by zahrnovat následující:

* Azure Event Hub nasazené do primární i sekundární oblasti.
* Geografická katastrofa povolena pro spárování primárního a sekundárního centra událostí.  Tím se také vytvoří "alias", který můžete použít k připojení k rozbočovačům událostí a přepnout z primárního na sekundární bez změny informací o připojení.
* Funkční aplikace nasazené do primární i sekundární oblasti.
* Aplikace funkcí se aktivují v *přímém* (nealias) připojovacím řetězci pro příslušné centrum událostí. 
* Vydavatelé centra událostí by měli publikovat do připojovacího řetězce aliasu. 

![Příkladová architektura active-pasivní](media/functions-geo-dr/active-passive.png)

Před převzetím služeb při selhání budou vydavatelé odesílající do sdíleného aliasu směrováni do primárního centra událostí.  Aplikace primární funkce naslouchá výhradně primárnímu centru událostí.  Aplikace sekundární funkce bude pasivní a nečinná.  Jakmile je zahájeno převzetí služeb při selhání, vydavatelé odesílající do sdíleného aliasu se nyní přesměrují do sekundárního centra událostí.  Aplikace sekundární funkce se nyní aktivuje a spustí se automaticky.  Efektivní převzetí služeb při selhání do sekundární oblasti lze řídit zcela z centra událostí, přičemž funkce se stanou aktivními pouze v případě, že je aktivní příslušné centrum událostí.

Přečtěte si další informace a důležité informace o převzetí služeb při selhání pomocí [služby Service Bus](../service-bus-messaging/service-bus-geo-dr.md) a [center událostí](../event-hubs/event-hubs-geo-dr.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření předních dveří Azure](../frontdoor/quickstart-create-front-door.md)
* [Důležité informace o převzetí služeb při selhání centra událostí](../event-hubs/event-hubs-geo-dr.md#considerations)
