---
title: Zprávy a připojení ve službě Azure SignalR
description: Přehled klíčových konceptů o zprávách a připojeních ve službě Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 5f6428231a3639738e8fb52e7dc3f2f2a3d2a26e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75392815"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Zprávy a připojení ve službě Azure SignalR

Model fakturace pro službu Azure SignalR je založen na počtu připojení a počtu zpráv. Tento článek vysvětluje, jak jsou definovány zprávy a připojení a počítá se pro fakturaci.


## <a name="message-formats"></a>Formáty zpráv 

Služba Azure SignalR service podporuje stejné formáty jako ASP.NET Core SignalR: [JSON](https://www.json.org/) a [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Velikost zpráv

Služba Azure SignalR nemá žádné omezení velikosti pro zprávy.

Velké zprávy jsou rozděleny do menších zpráv, které nejsou větší než 2 KB každý a přenášeny samostatně. Sady SDK zpracovávají rozdělení a sestavení zpráv. Není potřeba žádné úsilí vývojářů.

Velké zprávy negativně ovlivňují výkon zasílání zpráv. Použijte menší zprávy, kdykoli je to možné, a otestujte optimální velikost zprávy pro každý scénář použití.

## <a name="how-messages-are-counted-for-billing"></a>Jak se započítávají zprávy pro fakturaci

Pro fakturaci se počítají pouze odchozí zprávy ze služby Azure SignalR. Ping zprávy mezi klienty a servery jsou ignorovány.

Zprávy větší než 2 KB se počítají jako více zpráv 2 KB každý. Graf počtu zpráv na webu Azure Portal se aktualizuje každých 100 zpráv na rozbočovač.

Představte si například, že máte tři klienty a jeden aplikační server. Jeden klient odešle zprávu o 4 KB, aby server mohl vysílat všem klientům. Počet zpráv je osm: jedna zpráva ze služby na aplikační server a tři zprávy ze služby klientům. Každá zpráva se počítá jako dvě zprávy 2 KB.

## <a name="how-connections-are-counted"></a>Jak se počítají připojení

K dispozici jsou připojení k serveru a připojení klientů se službou Azure SignalR. Ve výchozím nastavení se každý aplikační server spustí s pěti počátečními připojeními na rozbočovač a každý klient má jedno připojení klienta.

Počet připojení zobrazený na webu Azure Portal zahrnuje připojení k serveru i připojení klientů.

Předpokládejme například, že máte dva aplikační servery a že definujete pět rozbočovačů v kódu. Počet připojení k serveru bude 50: 2 aplikační servery * 5 rozbočovačů * 5 připojení na rozbočovač.

ASP.NET SignalR vypočítá připojení k serveru jiným způsobem. Obsahuje jeden výchozí rozbočovač kromě rozbočovačů, které definujete. Ve výchozím nastavení potřebuje každý aplikační server dalších pět počátečních připojení k serveru. Počáteční počet připojení pro výchozí rozbočovač zůstane konzistentní s ostatními rozbočovači.

Během životnosti aplikačního serveru zachováslužba a aplikační server stav připojení synchronizace a provede úpravu připojení k serveru pro lepší výkon a stabilitu služby. Takže se může čas od času zobrazit změny čísla připojení k serveru.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Jak se počítá příchozí a odchozí provoz

Rozdíl mezi příchozí min. a odchozí provoz je založen na perspektivě služby Azure SignalR. Provoz se počítá v bajtů.

## <a name="related-resources"></a>Související prostředky

- [Typy agregace ve službě Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET konfigurace Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [Balíček zpráv](/aspnet/core/signalr/messagepackhubprotocol)
