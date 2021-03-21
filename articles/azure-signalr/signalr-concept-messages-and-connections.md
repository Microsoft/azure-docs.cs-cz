---
title: Zprávy a připojení ve službě Azure Signal Service
description: Přehled klíčových konceptů zpráv a připojení ve službě Azure Signal Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: zhshang
ms.openlocfilehash: 3c4d28addac0ecfc9605678582562550a1c96b8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491941"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Zprávy a připojení ve službě Azure Signal Service

Model fakturace služby Azure Signal je založen na počtu připojení a počtu zpráv. Tento článek vysvětluje, jak se definují a počítají zprávy a připojení pro účely fakturace.


## <a name="message-formats"></a>Formáty zpráv 

Služba signalizace Azure podporuje stejné formáty jako ASP.NET Core Signal: [JSON](https://www.json.org/) a [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Velikost zpráv

Služba signalizace Azure nemá omezení velikosti zpráv.

Velké zprávy jsou rozdělené na menší zprávy, které neobsahují více než 2 KB, a odesílají se samostatně. Sady SDK zpracovávají rozdělení a kompletování zpráv. Nejsou potřeba žádné úsilí pro vývojáře.

Velké zprávy mají negativní vliv na výkon zasílání zpráv. Kdykoli je to možné, používejte menší zprávy a při testování určete optimální velikost zprávy pro každý scénář použití.

## <a name="how-messages-are-counted-for-billing"></a>Jak se počítají zprávy pro účely fakturace

Pro účely fakturace se započítávají jenom odchozí zprávy ze služby Azure Signal. Zprávy na pøíkazech čekají mezi klienty a servery se ignorují.

Zprávy větší než 2 KB se počítají jako různé zprávy o velikosti 2 KB. Graf počtu zpráv v Azure Portal se aktualizuje každých 100 zpráv na střed.

Představte si například, že máte jeden aplikační server a tři klienty:

Aplikační server vysílá všem připojeným klientům zprávu od 1 KB. zpráva z aplikačního serveru do služby se považuje za bezplatnou příchozí zprávu. Jako odchozí zprávy se účtují jenom tři zprávy odesílané ze služby do každého klienta.

Klient A pošle zprávu o 1 KB jinému klientovi B bez přechodu na aplikační server. Zpráva od klienta A do služby je volná příchozí zpráva. Zpráva ze služby na klienta B se účtuje jako odchozí zpráva.

Pokud máte tři klienty a jeden aplikační server. Jeden klient pošle zprávu o 4 KB, aby mohl server vysílat všem klientům. Počet účtovaných zpráv je 8: jedna zpráva od služby k aplikačnímu serveru a tři zprávy od služby ke klientům. Každá zpráva se počítá jako zprávy 2 2-KB.

## <a name="how-connections-are-counted"></a>Jak se počítají připojení

K dispozici jsou připojení serveru a klientská připojení ke službě Azure Signal Service. Ve výchozím nastavení každý aplikační server začíná pět počátečních připojení na jeden rozbočovač a každý klient má jedno připojení klienta.

Předpokládejme například, že máte dva aplikační servery a definujete pět rozbočovačů v kódu. Počet připojení k serveru bude 50:2 aplikační servery * 5 rozbočovačů * 5 připojení na centrum.

Počet připojení zobrazený v Azure Portal zahrnuje připojení serveru, připojení klientů, diagnostická připojení a připojení živého trasování. Typy připojení jsou definované v následujícím seznamu:

- **Připojení k serveru**: propojuje službu signalizace Azure a aplikační server.
- **Připojení klienta**: propojuje službu a klientskou aplikaci Azure Signal.
- **Diagnostické připojení**: speciální typ připojení klienta, které může vytvořit podrobnější protokol, což může mít vliv na výkon. Tento druh klienta je určený k řešení potíží.
- **Připojení Live Trace**: připojuje se ke koncovému bodu Live Trace a přijímá živá trasování služby Azure Signal. 
 
Všimněte si, že připojení živého trasování se nepočítá jako připojení klienta nebo jako připojení k serveru. 

ASP.NET Signal vypočítává připojení serveru jiným způsobem. Zahrnuje jedno výchozí centrum navíc k rozbočovačům, které definujete. Ve výchozím nastavení každý aplikační server potřebuje pět dalších počátečních připojení k serveru. Počáteční počet připojení pro výchozí centrum zůstane v souladu s ostatními centry.

Služba a aplikační server udržují synchronizovaný stav připojení a provádí úpravy připojení serveru, aby dosáhly vyššího výkonu a stability služeb.  Takže se někdy může zobrazit číslo připojení k serveru od času.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Jak se počítají příchozí a odchozí přenosy

Zpráva odeslaná do služby je příchozí zpráva. Zpráva odeslaná mimo službu je odchozí zpráva. Provoz se počítá v bajtech.

## <a name="related-resources"></a>Související prostředky

- [Typy agregace v Azure Monitor](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr )
- [Konfigurace signalizace ASP.NET Core](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
