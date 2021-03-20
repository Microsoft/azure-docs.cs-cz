---
title: Azure Functions geografického zotavení po havárii a vysoké dostupnosti
description: Použití geografických oblastí pro zajištění redundance a převzetí služeb při selhání v Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 446a76b823ce3592a83d0c2f898041951361b47e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86506277"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions geografické zotavení po havárii

Když celé oblasti Azure nebo datová centra dostanou výpadky, je důležité, aby výpočetní prostředky pokračovaly v zpracování v jiné oblasti.  V tomto článku se vysvětlují některé strategie, které můžete použít k nasazení funkcí, které umožňují zotavení po havárii.

## <a name="basic-concepts"></a>Základní koncepty

Azure Functions spustit v konkrétní oblasti.  Chcete-li získat vyšší dostupnost, můžete stejné funkce nasadit do více oblastí.  Když ve více oblastech můžete mít vaše funkce spuštěné v *aktivním/aktivním* vzoru nebo v *aktivním/pasivním* vzoru.  

* Aktivní/aktivní vysoká dostupnost: Obě oblasti jsou aktivní a přijímají se události (jsou duplicitní nebo kruhové). Aktivní/aktivní se doporučuje pro funkce protokolu HTTPS v kombinaci s předními dvířky Azure.
* Aktivní/pasivní. Jedna oblast je aktivní a přijímá události, zatímco sekundární je nečinný.  Pokud je vyžadováno převzetí služeb při selhání, je sekundární oblast aktivována a převezme se zpracování.  Tato možnost se doporučuje pro jiné funkce než HTTP, jako je Service Bus a Event Hubs.

Další informace o nasazeních ve více oblastech najdete [v tématu spuštění aplikací v několika oblastech](/azure/architecture/reference-architectures/app-service-web-app/multi-region) .

## <a name="activeactive-for-https-functions"></a>Aktivní/aktivní pro funkce HTTPS

Pro zajištění aktivní/aktivní nasazení funkcí vyžaduje některá součást, která může koordinovat události mezi oběma oblastmi.  V případě funkcí protokolu HTTPS je tato koordinace prováděna pomocí služby [Azure front-dveří](../frontdoor/front-door-overview.md).  Přední dvířka Azure můžou směrovat požadavky HTTPS a kruhové dotazování mezi několika místními funkcemi.  Také pravidelně kontroluje stav každého koncového bodu.  Pokud místní funkce přestane reagovat na kontroly stavu, přední dveře Azure přestanou být v provozu a přesměrují jenom provoz do zdravých funkcí.  

![Architektura pro službu Azure front-dveří a funkci](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Aktivní/aktivní pro funkce jiného typu než HTTPS

K dispozici jsou i nadále aktivní/aktivní nasazení pro funkce bez HTTPS.  Je však třeba vzít v úvahu, jak budou tyto dvě oblasti vzájemně spolupracovat nebo se vzájemně koordinovat.  Pokud jste stejnou aplikaci Function App nasadili do dvou oblastí, každá aktivuje se ve stejné Service Busové frontě, která by mohla vystupovat jako konkurenční příjemce ve frontě.  To znamená, že každá zpráva je zpracovávána pouze jednou z instancí, znamená to také, že v jednom Service Bus je stále jediný bod selhání.  Pokud nasadíte dvě fronty Service Bus (jednu v primární oblasti, jednu v sekundární oblasti) a dvě aplikace funkcí odkazovaly na jejich oblast, zobrazí se výzva nyní ve způsobu, jakým jsou zprávy fronty distribuovány mezi dvě oblasti.  To často znamená, že se každý Vydavatel pokusí publikovat zprávy do *obou* oblastí a každá zpráva je zpracována aktivní funkcí aplikace.  V takovém případě se vytvoří model aktivní/aktivní a vytvoří se další výzvy týkající se duplikace výpočetních prostředků a při konsolidaci dat nebo způsobu jejich konsolidace.  Z těchto důvodů se doporučuje, aby triggery bez HTTPS používaly vzorek aktivní/pasivní.

## <a name="activepassive-for-non-https-functions"></a>Aktivní/pasivní pro funkce bez HTTPS

Aktivní/pasivní poskytuje způsob, jak každou zprávu zpracovat jenom jedna funkce, ale poskytuje mechanismus pro převzetí služeb při selhání do sekundární oblasti v případě havárie.  Azure Functions funguje souběžně s [Azure Service Bus geografickým obnovením](../service-bus-messaging/service-bus-geo-dr.md) a [geografickým obnovením Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md).

Pomocí aktivačních událostí Azure Event Hubs jako příklad může vzor aktivní/pasivní zahrnovat následující:

* Centrum událostí Azure je nasazené do primární i sekundární oblasti.
* Geografická havárie povolená pro spárování primárního a sekundárního centra událostí.  Tím se také vytvoří alias, pomocí kterého se můžete připojit k centrům událostí a přepnout z primárního na sekundární, aniž by se změnily informace o připojení.
* Aplikace Function App se nasazují do primární i sekundární oblasti.
* Aplikace Function App se aktivují v připojovacím řetězci *přímého* (bez aliasu) pro příslušné centrum událostí. 
* Vydavatelé do centra událostí by se měli publikovat do připojovacího řetězce aliasu. 

![Příklad architektury aktivní – pasivní](media/functions-geo-dr/active-passive.png)

Před převzetím služeb při selhání budou vydavatelé odesílající do sdíleného aliasu směrovat do primárního centra událostí.  Primární aplikace Function naslouchá výhradně primárnímu centru událostí.  Sekundární aplikace Function App bude pasivní a nečinný.  Po zahájení převzetí služeb při selhání budou vydavatelé odesílající do sdíleného aliasu přesměrováni do sekundárního centra událostí.  Sekundární aplikace Function App se teď aktivuje jako aktivní a spustí se automaticky.  Efektivní převzetí služeb při selhání u sekundární oblasti se dá zcela řídit z centra událostí. funkce se stanou aktivní jenom v případě, že je příslušné centrum událostí aktivní.

Přečtěte si další informace a požadavky pro převzetí služeb při selhání pomocí [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) a [centra událostí](../event-hubs/event-hubs-geo-dr.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření front-dveří Azure](../frontdoor/quickstart-create-front-door.md)
* [Event Hubs předpoklady pro převzetí služeb při selhání](../event-hubs/event-hubs-geo-dr.md#considerations)
