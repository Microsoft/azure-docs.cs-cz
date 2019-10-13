---
title: Zprávy a připojení ve službě Azure Signal Service
description: Přehled klíčových konceptů zpráv a připojení ve službě Azure Signal Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 2785d85db47ed3b214044e673566a2837b83e984
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285490"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Zprávy a připojení ve službě Azure Signal Service

Model fakturace služby Azure Signal je založen na počtu připojení a počtu zpráv. Tento článek vysvětluje, jak se definují a počítají zprávy a připojení pro účely fakturace.


## <a name="message-formats"></a>Formáty zpráv 

Služba signalizace Azure podporuje stejné formáty jako ASP.NET Core Signal: [JSON](https://www.json.org/) a [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Velikost zprávy

Služba signalizace Azure nemá omezení velikosti zpráv.

Velké zprávy jsou rozdělené na menší zprávy, které neobsahují více než 2 KB, a odesílají se samostatně. Sady SDK zpracovávají rozdělení a kompletování zpráv. Nejsou potřeba žádné úsilí pro vývojáře.

Velké zprávy mají negativní vliv na výkon zasílání zpráv. Kdykoli je to možné, používejte menší zprávy a při testování určete optimální velikost zprávy pro každý scénář použití.

## <a name="how-messages-are-counted-for-billing"></a>Jak se počítají zprávy pro účely fakturace

Pro účely fakturace se započítávají jenom odchozí zprávy ze služby Azure Signal. Zprávy na pøíkazech čekají mezi klienty a servery se ignorují.

Zprávy větší než 2 KB se počítají jako různé zprávy o velikosti 2 KB. Graf počtu zpráv v Azure Portal se aktualizuje každých 100 zpráv na střed.

Představte si například, že máte tři klienty a jeden aplikační server. Jeden klient pošle zprávu o 4 KB, aby mohl server vysílat všem klientům. Počet zpráv je 8: jedna zpráva od služby k aplikačnímu serveru a tři zprávy od služby ke klientům. Každá zpráva se počítá jako dvě zprávy o 2 KB.

Počet zpráv zobrazený v Azure Portal zůstane 0, dokud nebude nahromadění na více než 100.

## <a name="how-connections-are-counted"></a>Jak se počítají připojení

K dispozici jsou připojení serveru a klientská připojení ke službě Azure Signal Service. Ve výchozím nastavení každý aplikační server začíná pět počátečních připojení na jeden rozbočovač a každý klient má jedno připojení klienta.

Počet připojení zobrazený v Azure Portal zahrnuje připojení serveru i připojení klientů.

Předpokládejme například, že máte dva aplikační servery a že definujete pět rozbočovačů v kódu. Počet připojení k serveru bude 50:2 aplikační servery * 5 rozbočovačů * 5 připojení na centrum.

ASP.NET Signal vypočítává připojení serveru jiným způsobem. Zahrnuje jedno výchozí centrum navíc k rozbočovačům, které definujete. Ve výchozím nastavení každý aplikační server potřebuje pět dalších počátečních připojení k serveru. Počáteční počet připojení pro výchozí centrum zůstane v souladu s nastavením ostatních Center.

Během životnosti aplikačního serveru udržuje služba a aplikační server stav připojení synchronizace a usnadňuje úpravy připojení serveru pro lepší výkon a stabilitu služeb. Takže se někdy může zobrazit číslo připojení k serveru od času.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Jak se počítají příchozí a odchozí přenosy

Rozdíl mezi příchozím provozem a odchozím provozem je založený na perspektivě služby Azure Signal. Provoz se počítá v bajtech. Podobně jako u počtu zpráv má provoz také vzorkovací frekvenci. Příchozí/odchozí graf v Azure Portal se aktualizuje každých 100 KB na jeden hub.

## <a name="related-resources"></a>Související materiály

- [Typy agregace v Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Konfigurace signalizace ASP.NET Core](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
